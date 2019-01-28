

 使用的过程中，遇到的问题：

[UnCaught TypeError: img.toPng is not a function](https://github.com/knightli/markdown-assistant/issues/18)

解决：

/Users/xxx/.atom/packages/markdown-assistant/lib/main.coffee

把本地main.coffee文件中57行的img.toPng()改成img.toPNG()就行了
