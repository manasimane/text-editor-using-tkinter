# text-editor-using-tkinter
import tkinter
from tkinter import ttk
from tkinter import font, colorchooser, filedialog, messagebox  ## here we have imported everything which we have used below
import os
root=tkinter.Tk()
root.geometry("1200x800")
root.title("text editor")



##(((((((((((((((((((((((((((((((((((((((((#### main menu####)))))))))))))))))))))))))))))))))))))


main_menu=tkinter.Menu()

#####  file

file=tkinter.Menu(main_menu,tearoff=False)                         ## if we dont use tearoff then used it will tearoffing the menubars means it's getting apart after clinking on the line

url_variable=" "                                                    # we have created a global variable url ##every file have its own url and using that url we can get to know that the file exists or not if url is empty that means file does not exist and vice versa
#for new file
def new_file(event=None):                                          #we will bind it later with short keys so just for not getting an error we are writing something in paranthesis otherwise no need
    global url_variable                                            ## so that our global variable will change
    url_variable=" "                                               # here we are making our new file empty so that user can start writing again in new file
    text_editor_variable.delete(1.0,tkinter.END)
    
#for open function
def open_file(event=None):
    global url_variable
    url=filedialog.askopenfilename(initialdir=os.getcwd(),title="select file",filetypes=(("Text files",".txt"),("All files",".*")))# here we will ask to user which file they want to open and for that we have imported file dialog upward
                                                                   # here we will also use os module
                                                                   #os.getcsw means the current directory on which we are working that will be initial directory means the file will open only on that directory for which directory they have asked us before
    try:                                                           #sometimes when user didnt select any file then we get one error so here we will handle that error
       with open(url,"r") as f:                                    #when user will select any text file then we want read the data form that file and want to write that data in our file
          text_editor_variable.delete(1.0,tkinter.END)             #if something is alread written something in that file then we have to first delete that text
          text_editor_variable.insert(1.0, f.read())
    except FileNotFoundError:                                      ## in case file doesnt exist
           return
    except:                                                        ##in case user didnt open any file
           return
    root.title(os.path.basename(url_variable))                     ## title will change according the file selected by the user     
                                                                   ##basename will select the base name and that name will be shown as title
     
# for save
def save_file(event=None):
    global url_variable
    try:
        if url_variable:                                           ##if file already exists
            content=str(text_editor_variable.get(1.0,tkinter.END)) ##whatever we will write on the file we have stored all that in one variable
            with open(url_variable,"w",encoding="utf-8") as f:
               f.write(content)                                     ## whatever we have stored we all write in the file
        else:## in case of file doesnt exist
            url=filedialog.asksaveasfile(mode="w",defaultextension=".txt",filetypes=(("Text files",".txt"),("All files",".*")))
            content1=text_editor_variable.get(1.0,tkinter.END)
            url_variable.write(content1)
            url.close()
    except:                                                         #in case of any exception occurs
        return
        
       
        
# for save as
def save_as(event=None):
    global url_variable
    try:
        content = text_editor_variable.get(1.0, tkinter.END)
        url_variable=filedialog.asksaveasfile(mode="w",defaultextension=".txt",filetypes=(("Text files",".txt"),("All files",".*")))
        url_variable.write(content)                   
        url_variable.close()
    except:
        return
  
                               
file.add_command(label="New",accelerator="Ctrl+N",command=new_file)   ## here we have added shortcut using acceleraor but we can also write it in label
file.add_command(label="Open",accelerator="Ctrl+O",command=open_file)
file.add_command(label="Save",accelerator="Ctrl+S",command=save_file)
file.add_command(label="Save as",accelerator="Ctrl+Alt+S",command=save_as)

####  edit

#for copy,paste,cut,clear

edit=tkinter.Menu(main_menu,tearoff=False)

edit.add_command(label="Copy",accelerator="Ctrl+C",command=lambda:text_editor_variable.event_generate("<Control c>"))   ##using lambda and event generate with control c our data will be copy
edit.add_command(label="Paste",accelerator="Ctrl+V",command=lambda:text_editor_variable.event_generate("<Control v>"))##here data will get paste
edit.add_command(label="Cut",accelerator="Ctrl+X",command=lambda:text_editor_variable.event_generate("<Control x>"))
edit.add_command(label="Clear_all",accelerator="Ctrl+M",command=lambda: text_editor_variable.delete(1.0,tkinter.END))

#for find 
def find_func(event=None):
    def find():                                                      ## we can use different methods also but here we will use search method
        word=find_input.get()                                        #which word user want to find will be get here
        text_editor_variable.tag_remove("match","1.0",tkinter.END)   ## first we will remove the tag
        matches=0 
        if word:                                                     #in case user input any word
            start_position="1.0"  
            while True:                                              ##this loop will continue till we get the values 
                start_position=text_editor_variable.search(word,start_position,stopindex=tkinter.END)
                if not start_position:                                ## in case we dont any word
                     break                                            ## then we will break the condition
                end_position=f"{start_position}+{len(word)}c"
                text_editor_variable.tag_add("match", start_position, end_position) ## we can also use another method like we can gather all the words in a list and then after finding the positon of that words we can chnage the bg and fg of that words and we can use RegEx method
                matches+=1
                start_position=end_position
                text_editor_variable.tag_config("match",foreground="black",background="blue")
    def replace():
        word=find_input.get()                                         ##the word which user want to replace
        replace_text=replace_input.get()                              ## waht user want to write after replacing that will store in a variable
        content=text_editor_variable.get(1.0,tkinter.END)#
        new_content=content.replace(word,replace_text)                ##here we want to store new content which we want to replace
        text_editor_variable.delete(1.0,tkinter.END)                  ##here we will delete old content
        text_editor_variable.insert(1.0,new_content)                  ## here we will insert that new content
        
        
    find_window=tkinter.Toplevel()                                     #for the new window
    find_window.geometry("450x250+500+20")                             ##geometry of find window
    find_window.title("Find")
    find_window.resizable(0,0)                                        ##so that we cannot minimize or maximize the window
    
    ##now creating a label frame
    find_frame=ttk.LabelFrame(find_window,text="Find/Replace")
    find_frame.pack(pady=20)
    
    ##creating labels on this window
    find_label=ttk.Label(find_frame,text="find:")
    replace_label=ttk.Label(find_frame,text="replace:")
    find_label.grid(row=0,column=0,padx=4,pady=4)
    replace_label.grid(row=1,column=0,padx=4,pady=4)
    
    ##creating entry boxes
    find_input=ttk.Entry(find_frame,width=30)
    replace_input=ttk.Entry(find_frame,width=30)
    find_input.grid(row=0,column=1,padx=4,pady=4)
    replace_input.grid(row=1,column=1,padx=4,pady=4)
    

    ##creating buttons
    find_button=ttk.Button(find_frame,text="find",command=find)
    replace_button=ttk.Button(find_frame,text="replace",command=replace)
    find_button.grid(row=2,column=0,padx=4,pady=4)
    replace_button.grid(row=2,column=1,padx=4,pady=4)
    
    find_window.mainloop()

edit.add_command(label="Find",compound=tkinter.LEFT,accelerator="Ctrl+F",command=find_func)

#### color_theme

color_theme=tkinter.Menu(main_menu,tearoff=False)

##variables of color_theme
theme_choice=tkinter.StringVar()                               ## value which is selected by user will go in this variable 
                                                               ## using the colors we can change the background as well as forground color .....we can also write the code of the color and can also get it from online google picker
color_dict={ 
             " Light Default ": ("#000000", "#ffffff"),        ##first we have written the code for changing the color of text which is black and background color is white
             "Light_Plus"     : ("#474747", "#e0e0e0"),
             "Dark"           : ("#c4c4c4", "#2d2d2d"),
             "Red"            : ("#2d2d2d", "#ffe8e8"),
             "Monokai"        : ("#d3b774", "#474747"),
             "Night Blue"     : ("#ededed", "#6b9dc2")
           } 
def change_theme():
    theme_choosen=theme_choice.get()                           #the themes selected by user will store into this variable
    color_tuple=color_dict.get(theme_choosen)                  #using the key we will get our tuple
    fg_color,bg_color=color_tuple[0],color_tuple[1]            ##first foreground color and then tet color
    text_editor_variable.config(background=bg_color,fg=fg_color)
    
   
    
count=0                                                           ## here we used count to iterate the icons of theme
for i in color_dict:                                              ### here by default we will get keys but if we want both keys and values then we will use items method  ### here we are creating radio button so we are using loop so that we need not to write radio button for all icons again and again
    color_theme.add_radiobutton(label=i,variable=theme_choice, compound=tkinter.LEFT,command=change_theme) ## ere as the value of changes we will get the values of our themes then we use our tuple so we can use the icons one by one ##so tuple helps to reduce code we can also do that without using tuple ## we have used compound so that the label and icon should not override
    count+=1                                                      ##otherwise only white icon will show to evryone


help_label=tkinter.Menu(main_menu,tearoff=False)
def infoabout():
    root=tkinter.Tk()
    root.geometry("800x600")
    root.title("Information About Text Editor")
    label_frame=ttk.Label(root,text="HELLO..     \n\nINFORMATION ABOUT File option\n\n 1)Select New option to get new blank file \n You can also use Ctrl+N as shortcut to select file option\n\n 2)Select open option to open a file which exixts in your system\n You can also use Ctrl+O as shortcut to select Open option\n\n 3)Select Save option to save your file\n You can also use Ctrl+S as shortcut to select your Save option\n\n 4)Select Save as option to change name of your saved file \n You can also use Ctrl+Alt+S as shortcut to select your Save as option\n\n 5)Select Exit option if you want to exit\n You can also shortcut as Ctrl+Q to select Exit option \n INFORMATION ABOUT Edit option \n\n 1)Select Copy option to copy your selected text\n You can also use Ctrl+c as shortcut to select Cut option\n\n 2) Select Paste option to paste your selected text\n You can also use Ctrl+V as shortcut to select Paste option\n\n 3) Select Cut option to cut your select text\n you can also use Ctrl+X as shortcut to select Cut option\n\n 4) Select Clear_all to clear all text from your current file\n You can also use Ctrl+M as shortcut to select Clear_all option \n\n 5) Select Find option to select and replace your text throught the file\n You can also use Ctrl+F as shortcut to selet your Find option\n\n  INFORMATION ABOUT Color_theme\n   Here, when you want to edit background theme of your file then you can use this option. you can choose any theme which you want. \n\nYou have six options to select theme\n\n\n   THANK YOU!!!! ")  
    label_frame.pack()
    root.resizable(0,0)
    root.mainloop()
help_label.add_command(label="help",command=infoabout)

#now cascading it
main_menu.add_cascade(label="File", menu=file)
main_menu.add_cascade(label="Edit", menu=edit)
main_menu.add_cascade(label="Color_theme", menu=color_theme)
main_menu.add_cascade(label="Help",menu=help_label)


#(((((((((((((((((((((((((((((((((((((((((#### toolbar #####)))))))))))))))))))))))))))))))))))))))

#print(tkinter.font.families())## here we will get all font families it contain in form of tuple so we will use these fony families

## we want to make our toolbar at upside so we will make one label here and then we will add font combobox and font size in it
toolbar_label=ttk.Label(root)
toolbar_label.pack(side=tkinter.TOP,fill=tkinter.X)            ## tkinter.pop is used to keep at the top and we also want to fill it horizontally so will use  fill.X and if we want to fill it horizontally as well as vertically then we have to write fill.both and if here we used grid then we have to use north south something like that

## creaing font box
font_tuple=tkinter.font.families()
font_family=tkinter.StringVar()                                ##this will store the font selected by the user
font_combobox=ttk.Combobox(toolbar_label,width=30, textvariable=font_family, state="readonly")   ## here we have added our toolbar on the label                                  
font_combobox["values"]=font_tuple                             ##this is for the values which will be shown when user will select a something
font_combobox.current(font_tuple.index("Arial"))               ## so using our variable we can set index of any font which we want as our default  ##also we can use  in this  index of our tuple 
font_combobox.grid(row=0,column=0,padx=5)                      ## here we have given horizontal padding    


## for size box
size_variable=tkinter.IntVar()                                 ## this will store value selected by user
font_size=ttk.Combobox(toolbar_label, width=15, textvariable=size_variable, state="readonly" )
font_size["values"]=tuple(range(8,81))                         ## here also we are passing in which some values are there  ## we have given a range from which user can select the size and also we can give difference between two values 
font_size.current(font_size.index(4))                           ## default value of size
font_size.grid(row=0,column=1,padx=5)                                   


## creating buttons on the toolbar

##bold button
bold_btn=ttk.Button(toolbar_label,text="bold")
bold_btn.grid(row=0,column=2,padx=2)

##italic button
italic_btn=ttk.Button(toolbar_label,text="italic")
italic_btn.grid(row=0,column=3,padx=2)

#underline button
underline_btn=ttk.Button(toolbar_label,text="underline")
underline_btn.grid(row=0,column=4,padx=2)

#font color button
font_btn=ttk.Button(toolbar_label,text="font color")
font_btn.grid(row=0,column=5,padx=2)



#align left,right,center
alignleft_btn=ttk.Button(toolbar_label,text="left")
alignleft_btn.grid(row=0,column=7,padx=2)
aligncenter_btn=ttk.Button(toolbar_label,text="center")
aligncenter_btn.grid(row=0,column=8,padx=2)
alignright_btn=ttk.Button(toolbar_label,text="right")
alignright_btn.grid(row=0,column=9,padx=2)                     ##whenever we know that we to fi something at the top, bottom, left, right then we are using pack and when we want to fix something using row and column then we are using grid


##((((((((((((((((((((((((((((((((((((((((((((((((3 text editor)))))))))))))))))))))))))))))))))))))))))))))))))))


text_editor_variable=tkinter.Text(root)
text_editor_variable.config(wrap="word",relief=tkinter.FLAT)    ## wrap is for when we reach at the end of line and we have written any word which is not having enough space then that whole word will move down

## for the scrollbar
scrollbar_variable=tkinter.Scrollbar(root)                          ## we call to the constructor of scrollbar
text_editor_variable.focus_set()                                    ## for when user will open our application then they can directlt start typing, no need to move the cursor
scrollbar_variable.pack(side=tkinter.RIGHT, fill=tkinter.Y)
text_editor_variable.pack(fill=tkinter.BOTH,expand=True)             ## fill is used beacuse we want to fill both sides 
scrollbar_variable.config(command=text_editor_variable.yview)
text_editor_variable.config(yscrollcommand=scrollbar_variable.set)   ##for horizontal scrollbar

##functionality of font family and font size
current_font_family="Arial"                                          ## here we want whenever we start writing something then by default its font family anf font size will be Arial and 12 respectively
current_font_size=12                                                  ## these are the golbal variables

def change_font(event=None):                                          ## this function will change the font when we want ## here in the paranthesis we can write something else also like project=None so we dont must to add only none ...we have used bind so thats why if we dont pass anything in this paranthesis then we will get error
     global current_font_family                                       #here we have used global so that we can change the size and font in the our main application 
     current_font_family=font_family.get()                           ##we have already created the combobox for selecting the font so here we added functon in that so that when we select any font then we can get that font
     text_editor_variable.configure(font=(current_font_family,current_font_size))## here we change the font
font_combobox.bind("<<ComboboxSelected>>",change_font)                 ##here we bind our functionality to our main appliaction

def change_size(event=None):                                           ## this function will change the font when we want
     global current_font_size
     current_font_size=size_variable.get()                             ##we have already created the combobox for selecting the font so here we added functon in that so that when we select any font then we can get that font
     text_editor_variable.configure(font=(current_font_size,current_font_size))## here we cahnge the font
     
font_size.bind("<<ComboboxSelected>>",change_size)                      ##here we bind our functionality to our main appliaction

##### functionality of buttons
## 1) bold button 

#print(tkinter.font.Font(font=text_editor_variable['font']).actual()) ##here we set the value of font as text editor variable which we have declared upward then we pass a key font
                                                                      # so this object have a method and its name is actual if we print it then we get 
                                                                      #{'family': 'Courier New', 'size': 10, 'weight': 'normal', 'slant': 'roman', 'underline': 0, 'overstrike': 0}
                                                                      # so we are getting a dictionary which is giving us all the info about our text editor and we want to change its values

def bold():## here we are creating a function for bold   
    text_property=tkinter.font.Font(font=text_editor_variable['font'])
    if text_property.actual()["weight"]=="normal":                   ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"bold")) ## if text is in normal form then here we configure it and chnage it to bold
    if text_property.actual()["weight"]=="bold":                      ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"normal")) ## if text is in bold then here we changed it to normal 

bold_btn.configure(command=bold)

##functionality of italic button
def italic():                                                          ## here we are creating a function for bold   
    text_property=tkinter.font.Font(font=text_editor_variable['font'])
    if text_property.actual()["slant"]=="roman":                       ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"italic")) 
    if text_property.actual()["slant"]=="italic":                      ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"roman"))  
italic_btn.configure(command=italic)

##functionality of underlinebutton
def underline():                                                       ## here we are creating a function for bold   
    text_property=tkinter.font.Font(font=text_editor_variable['font'])
    if text_property.actual()["underline"]==0:                       ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"underline")) 
    if text_property.actual()["underline"]==1:                        ## here using square brackets we will change the values of our keys
       text_editor_variable.configure(font=(current_font_family,current_font_size,"normal")) 
underline_btn.configure(command=underline)

##functionality of font color
def font_color():
    color_variable=tkinter.colorchooser.askcolor()                       #here we make a variable which is equal to the the module which we have already imported that have a method ask color that will make a complete new color chooser window and the color selected by the user will store in this variable
    #text_editor.configure(fg=color)
    ##print(color_variable) when we print the variable then we get a tuple like this,
                            #((0.0, 0.0, 255.99609375), '#0000ff') we want to work with the hexa color so will use 1st index of this tuple
    text_editor_variable.configure(fg=color_variable[1])                       
font_btn.configure(command=font_color)


##functionality of align
#for left button
def align_left():
    textcontent_variable=text_editor_variable.get(1.0,'end')                ##this means from start till the end whatever we have written will get store in this variable
    text_editor_variable.tag_config("left",justify=tkinter.LEFT)
    text_editor_variable.delete(1.0,tkinter.END)                           #delete till the end
    text_editor_variable.insert(tkinter.INSERT,textcontent_variable,"left")
alignleft_btn.configure(command=align_left)

#for center button
def align_center():
    textcontent_variable=text_editor_variable.get(1.0,'end') 
    text_editor_variable.tag_config("center",justify=tkinter.CENTER)
    text_editor_variable.delete(1.0,tkinter.END)             
    text_editor_variable.insert(tkinter.INSERT,textcontent_variable,"center")
aligncenter_btn.configure(command=align_center)

#for right button
def align_right():
    textcontent_variable=text_editor_variable.get(1.0,'end')  
    text_editor_variable.tag_config("right",justify=tkinter.RIGHT)
    text_editor_variable.delete(1.0,tkinter.END)                
    text_editor_variable.insert(tkinter.INSERT,textcontent_variable,"right")
alignright_btn.configure(command=align_right)


#((((((((((((((((((((((((((((((((((((((((((((##  status bar))))))))))))))))))))))))))))))))))))))))))))

status_bar = ttk.Label(root,text="Status bar")
status_bar.pack(side=tkinter.BOTTOM)

text_changed=False 
def txt_changed(event=None):
    global text_changed
    if text_editor_variable.edit_modified():
        text_changed=True 
        words=len(text_editor_variable.get(1.0, 'end-1c').split())
        characters=len(text_editor_variable.get(1.0, 'end-1c'))
        status_bar.config(text=f'Characters : {characters} Words : {words}')
    text_editor_variable.edit_modified(False)
text_editor_variable.bind("<<Modified>>",txt_changed)    

    
    
#exit 
def exit_func(event=None):
    global url_variable,text_changed
    try:
        if text_changed:
            msgbox=messagebox.askyesnocancel("Warning","Do you want to save changes?")
            if msgbox is True:                                           #when we want to save changes #here if we dont write =>is true then when user get a warning window and if at that time user will select close than the file will also distroy
               if url_variable:                                          ## if the file aready exists
                    content=text_editor_variable.get(1.0,tkinter.END)
                    with open(url_variable,"w",encoding="utf-8") as f:
                        f.write(content)
                        root.destroy()
               else:
                   content1=str(text_editor_variable.get(1.0,tkinter.END))
                   url_variable=filedialog.asksaveasfile(mode="w",defaultextension=".txt",filetypes=(("Text files",".txt"),("All files",".*")))##in case file doesnt exixts then here we will ask user that where they want to save this file
                   url_variable.write(content1)
                   url_variable.close()
                   root.destroy()
            elif msgbox is False:                                      ## in case user select no
               root.destroy()
        else:                                                          ## in case user didnt save changes and want to exit 
          root.destroy()
    except:     
           return
       
file.add_command(label="Exit",compound=tkinter.LEFT,accelerator="Ctrl+Q",command=exit_func)
file.add_command(compound=tkinter.LEFT,command=txt_changed)   


root.config(menu=main_menu)

###binding shortcut keys
root.bind("<Control-n>",new_file)                                         #when we press control n then the the function which we have made for new file that will get call
root.bind("<Control-o>",open_file)                                        ##this is for open
root.bind("<Control-S>",save_file)
root.bind("<Control-Alt-S>",save_as)
root.bind("<Control-q>",exit_func)
root.bind("<Control-f>",find_func)

root.mainloop()
