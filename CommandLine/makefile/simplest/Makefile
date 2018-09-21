# 构建c.txt文件，需要准备好a.txt和b.txt文件
# 如果a.txt或者b.txt的修改时间，晚于c.txt，则按照后面的命令重新创建c.txt
# 如果c.txt不存在，则直接按照后面的命令，创建c.txt
c.txt: a.txt b.txt 
	# 第一个命令
	cat a.txt b.txt > c.txt
	# 第二个命令
	date >> c.txt

a.txt:
	echo "This is a." > a.txt
	date >> a.txt

.PHONY: clean
clean:
	rm a.txt