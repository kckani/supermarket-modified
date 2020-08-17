# supermarket-modified
from tkinter import *
from tkinter import messagebox
from tkinter import ttk
import sqlite3

class Database:
    def __init__(self, db):
        self.conn = sqlite3.connect(db)
        self.cur = self.conn.cursor()
        self.cur.execute(
            "CREATE TABLE IF NOT EXISTS Products (id INTEGER PRIMARY KEY, Product text, Prodtype text, AmountInStock text, Price text)")
        self.conn.commit()

    def fetch(self):
        self.cur.execute("SELECT * FROM Products")
        rows = self.cur.fetchall()
        return rows

    def insert(self, Product, Prodtype, AmountInStock, Price):
        self.cur.execute("INSERT INTO Products VALUES (NULL, ?, ?, ?, ?)",
                         (Product, Prodtype, Series, Brand, AmountInStock, Price))
        self.conn.commit()

    def remove(self, id):
        self.cur.execute("DELETE FROM Products WHERE id=?", (id,))
        self.conn.commit()

    def update(self, id, Product, Prodtype, Series, Brand, AmountInStock, Price):
        self.cur.execute("UPDATE Products SET Product = ?, Prodtype = ?, Series = ?, Brand = ?, AmountInStock = ?, Price = ? WHERE id = ?",
                         (Product, Prodtype, Series, Brand, AmountInStock, Price, id))
        self.conn.commit()

    def __del__(self):
        self.conn.close()

db = Database('store.db')


def populate_list():
    Products_list.delete(0, END)
    for row in db.fetch():
        Products_list.insert(END, row)


def add_item():
    if Product_text.get() == '' or Prodtype_text.get() == '' or Series_text.get() == '' or brand_text.get() == '' or AmountInStock_text.get() == '' or Price_text.get() == '':
        messagebox.showerror('Required Fields', 'Please include all fields')
        return
    db.insert(Product_text.get(), Prodtype_text.get(), Series_text.get(), brand_text.get(), AmountInStock_text.get(), Price_text.get())
    Products_list.delete(0, END)
    Products_list.insert(END, (Products_text.get(), Prodtype_text.get(), Series_text.get(), brand_text.get(), AmountInStock_text.get(), Price_text.get()))
    clear_text()
    populate_list()


def select_item(event):
    try:
        global selected_item
        index = Products_list.curselection()[0]
        selected_item = Products_list.get(index)

        Product_entry.delete(0, END)
        Product_entry.insert(END, selected_item[1])
        Prodtype_entry.delete(0, END)
        Prodtype_entry.insert(END, selected_item[2])
        Series_entry.delete(0, END)
        Series_entry.insert(END, selected_item[3])
        brand_entry.delete(0, END)
        brand_entry.insert(END, selected_item[4])
        AmountInStock_entry.delete(0, END)
        AmountInStock_entry.insert(END, selected_item[5])
        Price_entry.delete(0, END)
        Price_entry.insert(END, selected_item[6])
    except IndexError:
        pass


def remove_item():
    db.remove(selected_item[0])
    clear_text()
    populate_list()


def update_item():
    db.update(selected_item[0], Product_text.get(), Prodtype_text.get(), Series_text.get(), brand_text.get(),
              AmountInStock_text.get(), Price_text.get())
    populate_list()


def clear_text():
    Product_entry.delete(0, END)
    Prodtype_entry.delete(0, END)
    Series_entry.delete(0, END)
    brand_entry.delete(0, END)
    AmountInStock_entry.delete(0, END)
    Price_entry.delete(0, END)


# Create window object
app = Tk()

# Product
Product_text = StringVar()
Product_label = Label(app, text='Product Name', bg='red', font=('bold', 14))
Product_label.grid(row=0, column=0)
Product_entry = Entry(app, textvariable=Product_text)
Product_entry.grid(row=0, column=1)
# Prodtype
Prodtype_text = StringVar()
Prodtype_label = Label(app, text='Product Type', bg='blue', font=('bold', 14))
Prodtype_label.grid(row=1, column=0)
Prodtype_entry = Entry(app, textvariable=Prodtype_text)
Prodtype_entry.grid(row=1, column=1)
# Series
Series_text = StringVar()
Series_label = Label(app, text='Series', bg='magenta', font=('bold', 14))
Series_label.grid(row=2, column=0)
Series_entry = Entry(app, textvariable=Series_text)
Series_entry.grid(row=2, column=1)
# brand
brand_text = StringVar()
brand_label = Label(app, text='brand', bg='violet', font=('bold', 14))
brand_label.grid(row=3, column=0)
brand_entry = Entry(app, textvariable=brand_text)
brand_entry.grid(row=3, column=1)
# AmountInStock
AmountInStock_text = StringVar()
AmountInStock_label = Label(app, text='AmountInStock', bg='green', font=('bold', 14))
AmountInStock_label.grid(row=4, column=0)
AmountInStock_entry = Entry(app, textvariable=AmountInStock_text)
AmountInStock_entry.grid(row=4, column=1)
# Price
Price_text = StringVar()
Price_label = Label(app, text='Price', bg='grey', font=('bold', 14,))
Price_label.grid(row=5, column=0)
Price_entry = Entry(app, textvariable=Price_text)
Price_entry.grid(row=5, column=1)
# Products List (Listbox)
Products_list = Listbox(app, height=8, width=50, border=0, bg='yellow')
Products_list.grid(row=7, column=0, columnspan=3, rowspan=6, pady=20, padx=20)
# Create scrollbar
scrollbar = Scrollbar(app)
scrollbar.grid(row=6, column=6)
# Set scroll to listbox
Products_list.configure(yscrollcommand=scrollbar.set)
scrollbar.configure(command=Products_list.yview)
# Bind select
Products_list.bind('<<ListboxSelect>>', select_item)

# Buttons
add_btn = Button(app, text='Add Product', bg='white',width=12, command=add_item)
add_btn.grid(row=6, column=0, pady=20)

remove_btn = Button(app, text='Remove Product', bg='white',width=12, command=remove_item)
remove_btn.grid(row=6, column=1)

update_btn = Button(app, text='Update Product', bg='white',width=12, command=update_item)
update_btn.grid(row=6, column=2)

clear_btn = Button(app, text='Clear Input', bg='white',width=12, command=clear_text)
clear_btn.grid(row=6, column=3)

app.title('Supermarket Industry')
app.geometry('700x700')

# Populate data
populate_list()

# Start program
app.mainloop()
