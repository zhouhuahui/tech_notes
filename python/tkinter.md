# 错误

```python
g = Gui()
g.tx1.insert("insert","zhou") 
#_tkinter.TclError: invalid command name
```

因为g.tx1.insert()函数在Gui()对象撤销后执行，这时当然找不到tx1对象啦

# eg

```python
import tkinter as tk
from tkinter import filedialog
import os
import subprocess

class Gui:
    def __init__(self):
        # 初始化Tk()
        myWindow = tk.Tk()
        # 设置标题
        myWindow.title('SNLCompiler')
        # 设置窗口大小
        width = 760
        height = 600
        # 获取屏幕尺寸以计算布局参数，使窗口居屏幕中央
        screenwidth = myWindow.winfo_screenwidth()
        screenheight = myWindow.winfo_screenheight()
        alignstr = '%dx%d+%d+%d' % (width, height, (screenwidth - width) / 2, (screenheight - height) / 2)
        myWindow.geometry(alignstr)
        self.createContent(myWindow)
        myWindow.update()
        myWindow.mainloop()
    def createContent(self, root):
        but1 = tk.Button(root, text='select snl file', command=lambda:self.getSnlFile())
        but1.place(x=180, y=20, width=400, height=50)

        frame1 = tk.Frame(root, bg='red')

        #frame of lexical analysis--------------------------
        frame1.place(x=20, y=90, width=350, height=490)
        but2 = tk.Button(frame1, text='lexical analysis', command=lambda : self.lexical())
        but2.place(x=20, y=20, width=310, height=50)

        lab1 = tk.Label(frame1, text='tokenlist')
        lab1.place(x=20, y=90, width=310, height=25)
        self.tx1 = tk.Text(frame1, wrap="none")
        self.tx1.place(x=20, y=115, width=310, height=155)
        scroll1y = tk.Scrollbar(self.tx1)
        scroll1y.pack(side=tk.RIGHT, fill=tk.Y)
        scroll1y.config(command=self.tx1.yview)
        self.tx1.config(yscrollcommand=scroll1y.set)
        scroll1x = tk.Scrollbar(self.tx1, orient = tk.HORIZONTAL)
        scroll1x.pack(side=tk.BOTTOM, fill=tk.X)
        scroll1x.config(command=self.tx1.xview)
        self.tx1.config(xscrollcommand=scroll1x.set)

        lab2 = tk.Label(frame1, text='lexical error')
        lab2.place(x=20, y=290, width=310, height=25)
        self.tx2 = tk.Text(frame1, wrap="none")
        self.tx2.place(x=20, y=315, width=310, height=155)
        scroll2y = tk.Scrollbar(self.tx2)
        scroll2y.pack(side=tk.RIGHT, fill=tk.Y)
        scroll1y.config(command=self.tx2.yview)
        self.tx2.config(yscrollcommand=scroll2y.set)
        scroll2x = tk.Scrollbar(self.tx2, orient = tk.HORIZONTAL)
        scroll2x.pack(side=tk.BOTTOM, fill=tk.X)
        scroll2x.config(command=self.tx2.xview)
        self.tx2.config(xscrollcommand=scroll2x.set)

        # frame of syntax analysis--------------------------------
        frame2 = tk.Frame(root, bg='blue')
        frame2.place(x=390, y=90, width=350, height=490)
        but3 = tk.Button(frame2, text='syntax analysis', command=lambda : self.syntax())
        but3.place(x=20, y=20, width=310, height=50)

        lab3 = tk.Label(frame2, text='syntax analysis log')
        lab3.place(x=20, y=90, width=310, height=25)
        self.tx3 = tk.Text(frame2, wrap="none")
        self.tx3.place(x=20, y=115, width=310, height=355)
        scroll3y = tk.Scrollbar(self.tx3)
        scroll3y.pack(side=tk.RIGHT, fill=tk.Y)
        scroll3y.config(command=self.tx3.yview)
        self.tx3.config(yscrollcommand=scroll3y.set)
        scroll3x = tk.Scrollbar(self.tx3, orient = tk.HORIZONTAL)
        scroll3x.pack(side=tk.BOTTOM, fill=tk.X)
        scroll3x.config(command=self.tx3.xview)
        self.tx3.config(xscrollcommand=scroll3x.set)

        #变量
        self.snlFilePath = ""
    def getSnlFile(self):
        defaultDir = "D:\\"
        self.snlFilePath = filedialog.askopenfilename(title="select snl file", initialdir=(os.path.expanduser(defaultDir)))
    def lexical(self):
        exepath = "lexical_analysis.exe"
        if self.snlFilePath != "":
            exepath = exepath + " "
            exepath = exepath + self.snlFilePath
        p = subprocess.Popen(exepath, shell=True, stdout=subprocess.PIPE)
        p.wait()
        coutRes = p.stdout.readlines()
        self.tx1.delete(1.0, tk.END)
        self.tx2.delete(1.0, tk.END)
        if(str(coutRes[0], encoding='utf-8').startswith("error")):
            for line in coutRes:
                self.tx2.insert("insert", line)
        else:
            for line in coutRes:
                self.tx1.insert("insert",line)

    def syntax(self):
        exepath = "SyntaxAnalysis.exe"
        if self.snlFilePath != "":
            exepath = exepath + " "
            exepath = exepath + self.snlFilePath
        p = subprocess.Popen(exepath, shell=True)
        p.wait()
        self.tx3.delete(1.0, tk.END)
        with open("./CompilerData/AnalysisLog.txt","r") as f:
            lines = f.readlines()
            for line in lines:
                self.tx3.insert("insert",line)
if __name__ == '__main__':
    g = Gui()
```

