# 软件原理

### McPatch管理端工作流程(1.1)

1. 将工作空间目录和历史目录进行对比，找出被修改的文件列表
2. 将这些文件变动分为四类：1新增文件，2修改文件，3删除文件，4移动文件
3. 其中删除文件和移动文件比较好处理，直接在meta里记录对应的路径就行了
4. 对于1新增文件，2修改文件，除了打包文件长度和校验之类的meta，还要打包文件数据
   1. 新增文件直接打包完整的文件数据到更新包里（数据压缩使用bzip算法）
   2. 修改文件会对比新旧文件生成一个补丁，打包到更新包里（数据压缩使用bzip算法）
5. 收尾阶段时，将更新包meta和一个个文件的数据封装到zip格式的更新包里（注意封装不一定代表压缩）
6. 最后将更新包进行一次解压测试，然后添加版本号到版本列表文件里。到此版本（更新包）创建完成

### McPatch客户端工作流程(1.1)

1. 获取本地版本号和远程版本号，算出都落后了哪些版本
   1. 本地版本号直接读取版本号文件内容
   2. 远程版本号向服务端索要
2. 如果客户端已经是最新，那么程序退出，否则进入更新流程
3. 下载更新包，解压出里面的meta数据。根据文件变动类型的不同有不同的处理方法
   1. 新增文件：从更新包里解压出数据到对应的临时文件
   2. 修改文件：从更新包里解压出补丁文件，然后合并到对应的临时文件
      1. 如果文件被修改或者被删除，会跳过这个文件
   3. 删除文件：删除对应的本地文件
   4. 移动文件：移动对应的本地文件到新的位置
4. 所有文件全部解压或者处理好之后，会将上面的部分临时文件合并会原文件
5. 最后更新客户端当前版本
6. 如果有多个落后的版本，每个版本依次执行上面的操作
7. 最后弹出更新记录，程序结束运行

---

### McPatch管理端工作过程(1.0)

1. 将workspace目录下的文件与history目录进行对比，找出被修改的文件和目录
2. 对目录的修改（创建目录和删除目录）直接将相对路径写入元信息文件(.json)里
3. 对删除文件这种修改，也是直接将相对路径写入元信息文件(.json)里
4. 对文件内容发生变动的文件和完全新增的文件，进行下一步处理
5. 如果文件是完全新增的文件，那么使用bzip算法（压缩等级最低）压缩之后，将完整的二进制文件数据写入二进制数据文件里(.bin)
6. 如果是文件内容发生变动的文件，就使用bsdiff算法计算修改前后的文件差异，生成patch文件，将patch文件使用bzip算法（压缩等级默认）压缩之后的二进制数据，写入二进制数据文件(.bin)里
7. 在写进二进制数据文件(.bin)里时，会同时记录数据偏移值和长度，并将偏移值和长度写入元信息文件(.json)里以供客户端程序定位解压
8. 同时也会把旧文件hash，新文件hash，patch文件hash，patch文件压缩后的hash这四个hashes，都写入元信息文件(.json)里以供校验
9. 多次重复上面的过程，这样就记录好了所有文件修改，最后会计算一遍整个更新包的hash和长度，写入元信息文件(.json)里以供校验。到此，更新包就打包好了
10. 然后进入校验步骤，服务端在打包好了更新包之后，会接着对刚打好的更新包进行校验，会依次检测更新包本身hash和长度，再依次测试里面的每个文件的压缩后的数据hash，接着解压后再测试一遍解压后的hash，全部通过以后，在mc-patch-versions.txt里面加入刚创建的版本号，到此，创建版本过程就到此结束

### McPatch客户端工作过程(1.0)

1. 读取配置文件，下载服务端的mc-patch-versions.txt文件然后读取Jar文件旁边的mc-patch-version.txt文件，与之对比计算出当前版本与服务端落后多少个版本
2. 如果已是最新版本，则退出运行，否则进入更新流程
3. 客户端会下载缺失的版本的元信息文件(.json)并解析
4. 针对旧文件会直接删除，旧目录也会直接删除，新目录会直接创建对应目录
5. 而对于新文件的处理则会更加复杂
6. 新文件在服务端打包时，会根据修改类型被分类为3种类型：Empty，Fill，Modify
   1. Empty表示旧文件长度大于0，但是新文件长度等于0，对于这种文件客户端处理的方法是先删除再创建以空文件
   2. Fill表示旧文件长度等于0，但是新文件长度大于0，这种文件服务端在打包时会直接记录完整的文件内容。客户端从元信息文件(.json)里拿到这个文件的二进制压缩数据在二进制数据文件(.bin)里的偏移和长度之后，就可以开始提取了。先提取到内存里，然后进行一次校验，然后解压再写入到文件里，最后再进行一次文件校验
   3. Modify表示旧文件长度大于0，新文件长度也大于0。这种文件服务端在打包时不会直接记录完整的文件内容，而是记录一个补丁文件，使用这个补丁文件，就能将旧文件修补为新文件。客户端从元信息文件(.json)里拿到这个文件的二进制压缩数据段在二进制数据文件(.bin)里的偏移和长度之后，就可以开始提取了。注意这里提取的是patch数据，并不是完整的新文件数据。先提取到内存里，然后进行一次校验，然后在内存里解压，再进行一次文件校验。现在就取得了解压后的补丁文件。将这个补丁文件和旧文件进行修补，变成新文件之后，会再做最后一次hash校验
7. 最后弹出更新记录的窗口
8. 如果客户端落后多个版本，就把落下的版本依次执行上面的操作，直到更新到最新版本为止