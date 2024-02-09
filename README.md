# NEQR
import numpy as np
from qiskit import QuantumRegister, ClassicalRegister
from os import lstat
from re import L
from sys import displayhook
from qiskit import *
from qiskit import IBMQ
from qiskit.compiler import transpile, assemble
from qiskit.tools.jupyter import *
from qiskit.visualization import *
import matplotlib.pyplot as plt
from matplotlib import style
import pandas as pd
style.use('bmh')
import cv2
from qiskit import *
from PIL import Image
from qiskit.quantum_info import Statevector, Operator
style.use('bmh')
image_size = 256# Original image-width
#image_crop_size = 4  # Width of each part of image for processing
# Load the image from filesystem
#image = np.array(Image.open('C:/Users/yazahra/Desktop/New folder (2)/neqr2.jpg').convert('L'))
image = np.array(Image.open(r"C:\Users\User\Desktop\New folder (2)\neqr2.jpg").convert('L'))  
image1=image[:64,:64].copy()
image2=image[:64,64:128].copy()
image3=image[:64,128:192].copy()
image4=image[:64,192:256].copy()
image5=image[64:128,:64].copy()
image6=image[64:128,64:128].copy()
image7=image[64:128,128:192].copy()
image8=image[64:128,192:256].copy()         
def plot_image(img, title: str):
    plt.title(title)
    plt.xticks(range(0,img.shape[0]+1,32))
    plt.yticks(range(0,img.shape[1]+1,32))
    plt.imshow (img,extent=[0,img.shape[1],0,img.shape[0]] ,cmap='viridis')
    plt.show()
#plot_image(image_raw, 'landscape image')
# Initialize the quantum circuit for the image 
# Pixel position
idx = QuantumRegister(16, 'idx')
# grayscale pixel intensity value
intensity = QuantumRegister(8,'intensity')
# classical register
cr = ClassicalRegister(14, 'cr')
# create the quantum circuit for the image
qc = QuantumCircuit(intensity, idx,cr)
qc.i([0,1,2,3,4,5,6,7])
qc.h([8,9,10,11,12,13])
qc.barrier()
number=0
numcnot=0
pixel_list=[]
color_list=[]
for i in range(image_size):
    for j in range(image_size):
        number=1+number
        pixel=format(number-1,'b')
        element=len(pixel)
        zero=6-element
        #print(pixel)
        color=format(image[i][j],'b')
        pixel_list.append(pixel)
        color_list.append(color)
        #print (color)
        for counters,c in enumerate(color[::-1]):
             if (c=='1'):
               for counter,v in enumerate(pixel[::-1]):
                 if (v=='0'):
                   qc.x(counter+8)
                 if(zero!=0):
                    for m in range(element,6):
                      qc.x(m+8)  
               qc.mcx([8,9,10,11,12,13],counters)
               numcnot=numcnot+1
               for counter,v in enumerate(pixel[::-1]):
                if (v=='0'):
                   qc.x(counter+8)
                if(zero!=0):
                    for m in range(element,6):
                      qc.x(m+8)        
        qc.barrier() 
print(qc)
