import sys
import os
import re
import time
projectFolder = sys.argv[1]
#Need make some changes to fit your project
modifiedBy="Qing Feng"
authorName="Qing Feng"
version=""
description=""
copyrightYear="2016"
projectId='VT-123-123-123-123-123-123-123'
sourceCodeSuffix="\.java"
templateFile="wmTemplate.txt"
#

def getFileList(path):
    files=os.listdir(path)
    allFiles=[]
    for file in files:
        fullFileName=os.path.join(path,file)
        if os.path.isdir(fullFileName):
            subFolderFiles=getFileList(fullFileName)
            allFiles.extend(subFolderFiles)
        else:
            if(re.search(sourceCodeSuffix,fullFileName)):
                allFiles.append(fullFileName)    
    return allFiles

def generateWaterMark(wmTemplate):
    with open(wmTemplate, "r") as f:
        waterMark=f.read()

    waterMark=waterMark.replace("##MODIFIEDBY##",modifiedBy)

    waterMark=waterMark.replace("##PROJECTID##",projectId)
    waterMark=waterMark.replace("##AUTHORNAME##",authorName)

    waterMark=waterMark.replace("##VERSION##",version)
    waterMark=waterMark.replace("##DESCRIPTION##",description)
    waterMark=waterMark.replace("##COPYRIGHTYEAR##",copyrightYear)

    return waterMark
    
def addWaterMark(waterMark,fileName):
    
    with open(fileName, "r") as f:
        source=f.read()
        if re.search(projectId,source):
            return
        
    with open(fileName,"w") as f:
#        (mode, ino, dev, nlink, uid, gid, size, atime, mtime, ctime) = os.stat(fileName)
        mtime=os.path.getmtime(fileName)
        ctime=os.path.getctime(fileName)
        file=os.path.basename(fileName)
        waterMark=waterMark.replace("##FILENAME##",file)
        waterMark=waterMark.replace("##MODIFIEDDATE##",time.strftime("%Y-%m-%d",time.gmtime(mtime)))
        waterMark=waterMark.replace("##CREATIONDATE##",time.strftime("%Y-%m-%d",time.gmtime(ctime)))
        source=waterMark+"\n"+source
        f.write(source)

if __name__ == '__main__':
    if len(sys.argv)==2 and os.path.isdir(sys.argv[1]):
        waterMark=generateWaterMark(templateFile)
        for file in getFileList(projectFolder):
            addWaterMark(waterMark, file)
    else:
        print("\nUsage: watermark.py <Project folder path>\n\nExample: ./watermark.py /usr/test/myproject\n\t watermark.py c:\\user\\myproject\n\nFor a new project, you need to make some changes on watermark.py(From line 7 to line 14)")
