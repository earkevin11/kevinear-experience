from tkinter import *
from tkinter import messagebox
from random import choice, randint, shuffle
import pyperclip
import json

# ---------------------------- FIND PASSWORD ------------------------------- #
def find_password():
    website = website_input.get()
    try:
        with open("mydata.json") as data_file:
            # Read the old data file
            data = json.load(data_file)
    #if the file is not found, display a message box
    except FileNotFoundError:
        messagebox.showinfo(title="Error", message="No Data File found")
    #if the try block works, run this code
    else:
        if website in data:
            email = data[website]["email"]
            password = data[website]["password"]
            messagebox.showinfo(title=website,message=f"Email: {email}\nPassword: {password}")
        #if credentials for the specific website search does not exist
        else:
            messagebox.showinfo(title="Error", message="No credentials for Facebook exist. Please add.")



# ---------------------------- PASSWORD GENERATOR ------------------------------- #
def generate_password():

    letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
    numbers = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
    symbols = ['!', '#', '$', '%', '&', '(', ')', '*', '+']

    #You can change password requirements
    #range(9) not include 9.
    #List Comprension is easier than creating a for loop
    #Min letters in passwords is 8
    password_letters = [choice(letters) for n in range(randint(8, 10))]
    #Min symbols in password = 3
    password_symbols = [choice(symbols) for n in range(randint(2, 4))]
    # Min numbers in password = 3
    password_numbers = [choice(numbers) for n in range(randint(2, 4))]

    password_list = password_letters + password_symbols + password_numbers
    shuffle(password_list)
    password = "".join(password_list)
    password_input.insert(0,password)
    #this command automatically copies your password and you can just ctrl v or paste it without having to manually copy the password
    pyperclip.copy(password)

# ---------------------------- SAVE PASSWORD ------------------------------- #
def add_password():
    website = website_input.get()
    email = email_entry.get()
    password = password_input.get()
    new_data = {
        website: {
            "email": email,
            "password": password,
        }
    }

    if len(website) == 0 or len(password) == 0:
        messagebox.showerror(title="Error", message="Please do not leave fields empty!")
    else:
        try:
            with open("mydata.json", 'r') as data_file:
                # Read the old data file
                data = json.load(data_file)
        #if mydata.json file is not found, then create a file and dump the new_date that we entered in the GUI.
        except FileNotFoundError:
            with open("mydata.json", 'w') as data_file:
                # Saving updated data
                json.dump(new_data, data_file, indent=4)
        # if Try works then continue to update the file
        else:
            # Update the data_file with new_data that we entered in the GUI
            data.update(new_data)

            with open("mydata.json", 'w') as data_file:
                # Saving updated data
                json.dump(data, data_file, indent=4)
        #happens no matter what
        finally:
            website_input.delete(0, END)
            password_input.delete(0, END)

# ---------------------------- UI SETUP ------------------------------- #
window = Tk()
window.title("Kevin's Password Manager")
window.config(padx=40, pady=40)

canvas = Canvas(width=200, height=200,)
password_lock_img = PhotoImage(file="logo.png")
canvas.create_image(100,100,image=password_lock_img)
canvas.grid(column=1, row=0)

#labels
website_label = Label(text="Website:")
website_label.grid(column=0,row=1)

email_entry_label = Label(text="Email/Username:")
email_entry_label.grid(column=0,row=2)

password_label = Label(text="Password:")
password_label.grid(column=0,row=3)

#Entries
website_input = Entry(width=20)
website_input.grid(column=1,row=1)
#Automatically puts the cursor into the entry when you open the UI.
website_input.focus()

email_entry = Entry(width=39)
email_entry.grid(column=1,row=2,columnspan=2)
email_entry.insert(0,"earkevin11@gmail.com")

password_input = Entry(width=20)
password_input.grid(column=1,row=3)

# Generate Password Button
generate_button = Button(text="Generate Password",width=15,command=generate_password)
generate_button.grid(column=2, row=3)
# Add button
add_button = Button(text="Add", width=32,command=add_password)
add_button.grid(column=1,row=4,columnspan=2)

#Search button
search_button = Button(text="Search", width=5,command=find_password)
search_button.grid(column=2,row=1)



window.mainloop()
