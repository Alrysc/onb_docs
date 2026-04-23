# Deleting Mods

It takes one more step than you'd think to delete a mod.

## 1. Delete the ZIP

Find the ZIP and delete it. It'll be in the `mods` folder you put it in, based 
on the mod package type.

## 2. Delete the Extracted Folder

ONB will create a folder from the ZIP mod package by extracting it. You need to 
find and delete this folder (it'll be in the same `mods` folder as the ZIP, 
with the same name as the ZIP). 
If you don't, ONB will create a ZIP from the mod package's folder on boot. 

If you got caught by ONB regenerating the ZIP, make sure you also delete the 
ZIP again as you delete the folder.