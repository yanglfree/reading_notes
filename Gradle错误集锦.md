## Gradle错误集锦
Gradle编译的过程，根据环境的不同，配置信息的不同，会出现各种各样的错误，记录一下，以便后续查找
1. Invalid escape sequence at line 1 column 29 path $[0].name
编译报错：Invalid escape sequence at line 1 column 29 path $[0].name

解决：grade.properties 文件下 org.gradle.jvmargs=-Xmx1536m    替换 ：org.gradle.jvmargs=-Xmx4608m -Dfile.encoding=UTF-8
