####1.ls 命令 列出文件==
#####ls      直接输入ls命令，则列出当前目录下的所有文件和目录，不显示详细信息，如类型，大小，日期权限等。
#####ls -l　　每行显示一条记录，以长列表形式显示文件和目录的信息，包括文件类型、大小、修改日期和时间、权限、所属的用户和组等
#####ls -R　　将递归的列出某个目录的内容
#####ls -ls　　选项，将以长列表格式按文件大小顺序列出文件和目录（降序）
#####ls -a　　选项，将列出包括隐藏文件或目录在内的所有文件和目录，包括“.”(当前目录)和“..”（父目录）
#####ls -A　　列出包括隐藏文件或目录在内的所有文件和目录，不包括“.”(当前目录)和“..”（父目录）
#####ls -la　　列出当前目录下的所有文件和文件夹
#####ls a* 　　列出当前目录下所有以a字母开头的文件
#####ls -l *.txt　　列出当前目录下所有后缀名为txt的文件#####

####2.cp 命令 复制 
#####cp file /usr/men/tmp/file1　　文件file复制到目录/usr/men/tmp下，并改名为file1
#####cp -r /usr/men /usr/zh　　目录/usr/men下的所有文件及其子目录复制到目录/usr/zh中
#####cp -i /usr/men m*.c /usr/zh 　　交互式地将目录/usr/men中的以m打头的所有.c文件复制到目录/usr/zh中
#####cp file1 file2　　将文件file1复制成文件file2
#####cp -i file1 file2　　采用交互方式将文件file1复制成文件file2
#####cp -R file1 file2　　将目录dir1复制成目录dir2
#####cp -R file1 file2 file3 dir1 dir2　　同时将文件file1、file2、file3与目录dir1复制到dir2
#####cp -r -a aaa/* /bbb 　　复制目录aaa下所有到/bbb目录下,我们在Linux下使用cp命令复制文件时候，有时候会需要覆盖一些同名文件，覆盖文件的时候都会有提示：需要不停的按Y来确定执行覆盖。文件数量不多还好，但是要是几百个估计按Y都要吐血
####3.cat 命令 查看 组合文件
cat a.txt：查看文件的内容
cat a.txt >> b.txt：把a文件的内容组合到b文件内容的末尾
cat -n a.txt：查看文件并给文件标上行号
####4.touch命令 建立文件
touch a.txt：建立一个名为a的txt类型文件
####5. rm命令 删除文件
rm -rf a.txt：强制删除文件a.txt
tm -i a.txt：删除文件前会有提示是否确定删除该文件
####6.mkdir命令：
mkdir test：创建一个名为test的目录
####7.rmdir命令：删除目录
tmdir test：删除一个目录
####8.echo、cat命令：添加内容
echo “hello world!” >> a.txt：添加内容到文件a里面

cat <<EOF>> a.txt : 可以添加多行语句到文件本身内容的末尾

cat <<EOF> a.txt：添加内容到文件并覆盖到原始的内容
####9.grep命令：搜索文件
ls -la | grep a.txt ：搜索a.txt文件
####10.find命令：查找文件和目录
find命令：查找文件和目录

##### $ ps aux | grep python
查到python 的进程



