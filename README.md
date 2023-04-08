# Simple-game
This is simple game written on Python
#создай тут фоторедактор Easy Editor!
from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QPushButton, QVBoxLayout,QHBoxLayout,QRadioButton,QMessageBox,QGroupBox,QButtonGroup,QListWidget,QTextEdit,QFileDialog
import os
from PyQt5.QtGui import QPixmap
from PIL import Image,ImageFilter
app = QApplication([])
win = QWidget()
win.resize(700,500)
win.setWindowTitle('Easy Editor')
lb_image = QLabel()
btn_dir = QPushButton('Папка')
lw_files = QListWidget()
btn_bw = QPushButton('Ч/Б')
btn_left = QPushButton('Лево')
btn_right = QPushButton('Право')
btn_flip = QPushButton('Зеркало')
btn_sharp = QPushButton('Резкость')
row = QHBoxLayout()
col1 = QVBoxLayout()
col1.addWidget(btn_dir)
col1.addWidget(lw_files)
col2 = QVBoxLayout()
line1 = QHBoxLayout()
line1.addWidget(btn_bw)
line1.addWidget(btn_right)
line1.addWidget(btn_left)
line1.addWidget(btn_flip)
line1.addWidget(btn_sharp)
col2.addWidget(lb_image, 95)
col2.addLayout(line1)
row.addLayout(col1,20)
row.addLayout(col2,80)

workdir = ''

def chooseWorkdir():
    global workdir
    workdir = QFileDialog.getExistingDirectory()
def filter(filenames,extensions):
    result = []
    for filename in filenames:
        for ext in extensions:
            if filename.endswith(ext):
                result.append(filename)
    return result
def showfilenamesList():
    extensions  = ['.jpg','.jpeg','.png','.gif','.webp','.tif']
    chooseWorkdir()
    filenames = filter(os.listdir(workdir),extensions)
    lw_files.clear()
    for filename in filenames:
        lw_files.addItem(filename)
class ImageProccesor():
    def __init__(self):
        self.image = None
        self.dir = None
        self.filename = None
        self.save_dir = "Modified/"
    def showImage(self,path):
        lb_image.hide()
        pixmapimage = QPixmap(path)
        W,H  = lb_image.width(), lb_image.height()
        pixmapimage = pixmapimage.scaled(W,H,Qt.KeepAspectRatio)
        lb_image.setPixmap(pixmapimage)
        lb_image.show()
    def loadImage(self,filename):
        self.filename = filename
        image_path = os.path.join(workdir,filename)
        self.image = Image.open(image_path)
    def bwed(self):
        self.image = self.image.convert("L")
        self.saveImage()
        image_path = os.path.join(workdir, self.save_dir,self.filename) 
        self.showImage(image_path)
    def flipped(self):
        self.image = self.image.transpose(Image.FLIP_LEFT_RIGHT)
        self.saveImage()
        image_path = os.path.join(workdir, self.save_dir,self.filename) 
        self.showImage(image_path)
    def rightten(self):
        self.image = self.image.transpose(Image.ROTATE_270)
        self.saveImage()
        image_path = os.path.join(workdir, self.save_dir,self.filename) 
        self.showImage(image_path)
    def leftten(self):
        self.image = self.image.transpose(Image.ROTATE_90)
        self.saveImage()
        image_path = os.path.join(workdir, self.save_dir,self.filename) 
        self.showImage(image_path)
    def sharpen(self):
        self.image = self.image.filter(ImageFilter.SHARPEN)
        self.saveImage()
        image_path = os.path.join(workdir, self.save_dir,self.filename) 
        self.showImage(image_path)
    def saveImage(self):
        path = os.path.join(workdir,self.save_dir)
        if not(os.path.exists(path) or os.path.isdir(path)):
            os.mkdir(path)
        image_path = os.path.join(path,self.filename)
        self.image.save(image_path)
    def showChosenImage():
        if lw_files.currentRow() >= 0:
            filename = lw_files.currentItem().text()
            workimage.loadImage(filename)
            image_path = os.path.join(workdir, workimage.filename)
            workimage.showImage(image_path)
    lw_files.currentRowChanged.connect(showChosenImage)
workimage = ImageProccesor()
btn_bw.clicked.connect(workimage.bwed)
btn_flip.clicked.connect(workimage.flipped)
btn_right.clicked.connect(workimage.rightten)
btn_left.clicked.connect(workimage.leftten)
btn_sharp.clicked.connect(workimage.sharpen)
    
btn_dir.clicked.connect(showfilenamesList)

win.setLayout(row)
win.show()
app.exec()
