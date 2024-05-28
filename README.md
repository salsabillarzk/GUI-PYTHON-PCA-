# GUI PYHTON PCA (PRINCIPAL COMPONENT ANALYSIS)

```
#Install Package
import tkinter.messagebox
import tkinter as tk
from tkinter import *
from tkinter import Tk, Label, Button, Entry, IntVar, END, W, E
from tkinter import filedialog, messagebox, ttk, Frame, Menu
from tkinter import scrolledtext
import pandas as pd
import numpy as np
from scipy.stats import mode
import sklearn
import os
from sklearn.decomposition import PCA
!pip install factor_analyzer


window = tk.Tk()
window.title("ANALISIS KOMPONEN UTAMA (PCA)")
window.configure(background="#233E6F")
window.geometry("1200x625")
window.pack_propagate(False) 
window.resizable(0,0)


#IMPOR FILE EXCEL
upload_data = tk.LabelFrame(window, text="Impor Data (Format .xlsx)",bg='#FFFCE5')
upload_data.place(height=85, width=810,y=10, relx=0.01)
button_upload = tk.Button(upload_data, text="Import Data",background='#C5D4FF',command=lambda: File_dialog())
button_upload.place(rely=0.46, relx=0.1,width=75)
button_load = tk.Button(upload_data, text="Load Data",background='#C5D4FF',command=lambda: Import_excel_data())
button_load.place(rely=0.46, relx=0.42,width=75)
button_reset = tk.Button(upload_data, text="Reset",background='#C5D4FF',command=lambda: reset_data())
button_reset.place(rely=0.46, relx=0.76,width=75)
ket_file = tk.Label(upload_data, text="Tidak Ada File yang Dipilih",bg='#FFFCE5')
ket_file.place(rely=0, relx=0)


#IDENTITAS
idt_frame = tk.LabelFrame(window, text="Identitas",bg='#FFFCE5')
idt_frame.place(height=85, width=360,x=830, y=10)
idt_file = ttk.Label(idt_frame, background='#FFFCE5',text="Salsabilla Rizka Ardhana \n24050119120035 \nKomputasi Statistika Lanjut A",font=('Times New Roman',11,'bold'))
idt_file.place(rely=0.05, relx=0.01)


#TAMPILAN DATA
frame_data = tk.LabelFrame(window, text="Data",background='#FFFCE5')
frame_data.place(height=520, width=520,relx=0.01, rely=0.16)
treeview1 = ttk.Treeview(frame_data)
treeview1.place(relheight=25, relwidth=25) 
tscrolly = tk.Scrollbar(frame_data, orient="vertical", command=treeview1.yview) 
tscrollx = tk.Scrollbar(frame_data, orient="horizontal", command=treeview1.xview) 
treeview1.configure(xscrollcommand=tscrollx.set, yscrollcommand=tscrolly.set) 
tscrollx.pack(side="bottom", fill="x")  
tscrolly.pack(side="right", fill="y")
pd.set_option('display.max_columns', 500)


#ENTRY JUMLAH FAKTOR
frame_entry = tk.LabelFrame(window, text="Entry",background='#FFFCE5')
frame_entry.place(height=53,width=649, relx=0.45, rely=0.16)
entry1=tk.Entry(frame_entry)
entry1.place(rely=0.03, relx=0.3,width=400)
font=('times', 9, 'bold')
a2= Label(frame_entry, text='Jumlah Faktor', bg='#FFFCE5', fg='black', font=font)
a2.place(relx=0.17,rely=0.18,relheight=0.26, anchor='n')


#MENU PENGUJIAN
frame_pengujian=tk.LabelFrame(window, text="Menu",background='#FFFCE5')
frame_pengujian.place(height=60, width=649,relx=0.45, rely=0.255)
button_normal = tk.Button(frame_pengujian, text="Uji Asumsi",background='#C5D4FF',command=lambda: uji_asumsi())
button_normal.place(rely=0.02, relx=0.01, width=182)
button_stabilitas = tk.Button(frame_pengujian, text="Analisis Komponen Utama (PCA)",background='#C5D4FF',command=lambda: PCA())
button_stabilitas.place(rely=0.02, relx=0.35, width=182)
button_clear = tk.Button(frame_pengujian, text="Clear",background='#C5D4FF',command=lambda: clear_hasil())
button_clear.place(rely=0.02, relx=0.7, width=182)

#HASIL ANALISIS
hasil_frame = tk.LabelFrame(window, text="Hasil Analisis",background='#FFFCE5')
hasil_frame.place(height=395, width=649,relx=0.45,rely=0.36)



def File_dialog():
    filename = filedialog.askopenfilename(initialdir="/",
                                          title="Pilih File",
                                          filetype=(("xlsx files", ".xlsx"),("All Files", ".*")))
    ket_file["text"] = filename
    return None

    
def Import_excel_data():
    file_path = ket_file["text"]
    global df
    try:
        excel_filename = r"{}".format(file_path)
        if excel_filename[-4:] == ".csv":
            df = pd.read_csv(excel_filename)
        else:
            df = pd.read_excel(excel_filename)

    except ValueError:
        tk.messagebox.showerror("Information", "The file you have chosen is invalid")
        return None
    except FileNotFoundError:
        tk.messagebox.showerror("Information", "No such file as {file_path}")
        return None
 
    reset_data()
    treeview1["column"] = list(df.columns)
    treeview1["show"] = "headings"
    for column in treeview1["columns"]:
        treeview1.heading(column, text=column) # let the column heading = column name

    df_rows = df.to_numpy().tolist() # turns the dataframe into a list of lists
    for row in df_rows:
        treeview1.insert("", "end", values=row) # inserts each list into the treeview. 
    return None


def reset_data():
    treeview1.delete(*treeview1.get_children())
    return None

def uji_asumsi():
    #UJI KMO MSA
    global df
    from factor_analyzer.factor_analyzer import calculate_kmo
    kmo=calculate_kmo(df)
    label_KMO=tk.Label(hasil_frame,text=kmo)
    label_KMO.place(relx=0.03,rely=0.15,width=600)
    label_ketKMO=Label(hasil_frame, text='UJI KMO MSA',bg='#FFFCE5', fg='black', font=('Times New Roman', 14))
    label_ketKMO.place(relx=0.4,rely=0.01)  
    #UJI BARTLETT
    from factor_analyzer.factor_analyzer import calculate_bartlett_sphericity
    bartlett=calculate_bartlett_sphericity(df)
    label_bartlett=tk.Label(hasil_frame,text=bartlett)
    label_bartlett.place(relx=0.03,rely=0.57,width=600)
    label_ketbart=tk.Label(hasil_frame, text='UJI BARTLETT',bg='#FFFCE5', fg='black', font=('Times New Roman', 14))
    label_ketbart.place(relx=0.4,rely=0.45)  
    return None
    
def PCA():
    global df
    global entry1
    #Jumlah Faktor
    from factor_analyzer import FactorAnalyzer
    faktor=int(entry1.get())
    fact=FactorAnalyzer(faktor,rotation="varimax")
    hasil=fact.fit(df)
    loadings=fact.loadings_
    eigenvalues= fact.get_eigenvalues()
    label_jumlahfaktor=Label(hasil_frame, text=eigenvalues, font=('Times New Roman', 9))
    label_jumlahfaktor.place(rely=0.15, relx=0.03,width=600)
    label_ketfaktor=Label(hasil_frame, text='JUMLAH FAKTOR',bg='#FFFCE5', fg='black', font=('Times New Roman', 14))
    label_ketfaktor.place(relx=0.4,rely=0.01) 
    #Faktor Utama
    from sklearn.preprocessing import StandardScaler
    from sklearn.preprocessing import MinMaxScaler
    scaler=StandardScaler()
    scaler.fit(df)
    scaled_data=scaler.transform(df)
    # Menjalankan algoritma PCA
    from sklearn.decomposition import PCA
    faktor=int(entry1.get())
    pca = PCA(n_components=faktor)
    pca.fit(scaled_data)
    x_pca=pca.transform(scaled_data)
    x_pcacomp=pca.components_
    label_pca=tk.Label(hasil_frame,text=x_pcacomp)
    label_pca.place(rely=0.57,relx=0.03,width=600)
    label_ketfaktoru=Label(hasil_frame, text='FAKTOR UTAMA',bg='#FFFCE5', fg='black', font=('Times New Roman', 14))
    label_ketfaktoru.place(relx=0.4,rely=0.45) 
    
def clear_hasil():
    for widgets in hasil_frame.winfo_children():
        widgets.destroy()
    return None

def on_closing():
    if tkinter.messagebox.askokcancel("Halaman Keluar", "Apakah Anda Yakin Ingin Meninggalkan Halaman Ini?"):
        window.destroy()
        os._exit(0)

window.protocol("WM_DELETE_WINDOW", on_closing)
window.mainloop()
```
