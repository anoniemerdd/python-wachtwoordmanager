# python-wachtwoordmanager
Een python script wat door middel van de functie Tkinter een wachtwoordmanager vormt.


# Het standaard hoofdwachtwoord (initieel)
master_password_hash = hashlib.sha256(**"Standaardwachtwoord"**.encode()).hexdigest()

Verander "Standaardwachtwoord" naar je eigen gewenste wachtwoord. (Vergeet niet op te slaan)

import tkinter as tk
from tkinter import messagebox, Listbox, Scrollbar, simpledialog
import json
import hashlib

# Het standaard hoofdwachtwoord (initieel)
master_password_hash = hashlib.sha256("Standaardwachtwoord".encode()).hexdigest()


# Een lege lijst om wachtwoorden op te slaan
passwords = []


# Functie om het hoofdwachtwoord te controleren
def check_master_password():
    while True:
        master_password = simpledialog.askstring("Inloggen", "Voer het hoofdwachtwoord in:")
        if master_password is None:
            exit()  # Als de gebruiker annuleert, sluit het programma
        if hashlib.sha256(master_password.encode()).hexdigest() == master_password_hash:
            break
        else:
            messagebox.showerror("Fout", "Ongeldig hoofdwachtwoord. Probeer opnieuw.")

    window.withdraw()  # Verberg het venster tijdelijk
    window.update()    # Forceer een update om te zorgen dat het venster wordt verborgen voordat het weer wordt weergegeven
    window.deiconify()  # Breng het venster weer naar voren


# Een lege lijst om wachtwoorden op te slaan
passwords = []


# Functie om het hoofdwachtwoord te controleren
def check_master_password():
    while True:
        master_password = simpledialog.askstring("Inloggen", "Voer het hoofdwachtwoord in:")
        if master_password is None:
            exit()  # Als de gebruiker annuleert, sluit het programma
        if hashlib.sha256(master_password.encode()).hexdigest() == master_password_hash:
            break
        else:
            messagebox.showerror("Fout", "Ongeldig hoofdwachtwoord. Probeer opnieuw.")
    
    window.withdraw()  # Verberg het venster tijdelijk
    window.update()    # Forceer een update om te zorgen dat het venster wordt verborgen voordat het weer wordt weergegeven
    window.deiconify()  # Breng het venster weer naar voren


# Functie om het hoofdwachtwoord te wijzigen
def change_master_password():
    global master_password_hash
    while True:
        current_password = simpledialog.askstring("Wijzig hoofdwachtwoord", "Voer het huidige hoofdwachtwoord in:")
        if current_password is None:
            return  # Gebruiker heeft geannuleerd
        if hashlib.sha256(current_password.encode()).hexdigest() == master_password_hash:
            new_password = simpledialog.askstring("Wijzig hoofdwachtwoord", "Voer het nieuwe hoofdwachtwoord in:")
            if new_password is None:
                return  # Gebruiker heeft geannuleerd
            # Hier wordt het nieuwe hoofdwachtwoord opgeslagen als een hashwaarde
            master_password_hash = hashlib.sha256(new_password.encode()).hexdigest()
            messagebox.showinfo("Success", "Hoofdwachtwoord gewijzigd.")
            return
        else:
            messagebox.showerror("Fout", "Ongeldig huidig hoofdwachtwoord. Probeer opnieuw.")


# Functie om een nieuw wachtwoord toe te voegen
def add_password():
    website = website_entry.get()
    username = username_entry.get()
    password = password_entry.get()

    passwords.append({"website": website, "username": username, "password": password})
    clear_entries()
    save_passwords()
    update_password_list()
    messagebox.showinfo("Success", "Wachtwoord toegevoegd!")


# Functie om de wachtwoordlijst bij te werken
def update_password_list():
    password_listbox.delete(0, tk.END)
    for entry in passwords:
        password_listbox.insert(tk.END, entry["website"])


# Functie om wachtwoorden op te slaan in een JSON-bestand
def save_passwords():
    with open("passwords.json", "w") as file:
        json.dump(passwords, file)


# Functie om de invoervelden te wissen
def clear_entries():
    website_entry.delete(0, tk.END)
    username_entry.delete(0, tk.END)
    password_entry.delete(0, tk.END)


# Functie om een wachtwoord te selecteren en de bijbehorende gegevens weer te geven
def show_selected_password(event):
    selected_index = password_listbox.curselection()
    if selected_index:
        index = int(selected_index[0])
        selected_password = passwords[index]
        messagebox.showinfo("Wachtwoordgegevens", f"Website: {selected_password['website']}\nGebruikersnaam: {selected_password['username']}\nWachtwoord: {selected_password['password']}")


# Functie om een geselecteerde entry te verwijderen
def remove_selected_password():
    selected_index = password_listbox.curselection()
    if selected_index:
        index = int(selected_index[0])
        del passwords[index]
        save_passwords()
        update_password_list()
        messagebox.showinfo("Success", "Wachtwoord verwijderd.")


# GUI-initialisatie
window = tk.Tk()
window.title("Wachtwoordmanager")


# Vraag om het hoofdwachtwoord
check_master_password()


# Label en invoerveld voor de website
website_label = tk.Label(window, text="Website:")
website_label.pack()
website_entry = tk.Entry(window)
website_entry.pack()


# Label en invoerveld voor de gebruikersnaam
username_label = tk.Label(window, text="Gebruikersnaam:")
username_label.pack()
username_entry = tk.Entry(window)
username_entry.pack()


# Label en invoerveld voor het wachtwoord
password_label = tk.Label(window, text="Wachtwoord:")
password_label.pack()
password_entry = tk.Entry(window, show="*")  # Toont asterisken in plaats van wachtwoord
password_entry.pack()


# Knop om een nieuw wachtwoord toe te voegen
add_button = tk.Button(window, text="Toevoegen", command=add_password)
add_button.pack()


# Lijstbox om de opgeslagen websites weer te geven
password_listbox = Listbox(window, width=40)
password_listbox.pack()
password_listbox.bind("<<ListboxSelect>>", show_selected_password)


# Scrollbar voor de lijstbox
scrollbar = Scrollbar(window)
scrollbar.pack(side=tk.RIGHT, fill=tk.Y)
password_listbox.config(yscrollcommand=scrollbar.set)
scrollbar.config(command=password_listbox.yview)


# Knop om een geselecteerde entry te verwijderen
remove_button = tk.Button(window, text="Verwijder", command=remove_selected_password)
remove_button.pack()


# Laad opgeslagen wachtwoorden bij het opstarten (als er een bestand is)
try:
    with open("passwords.json", "r") as file:
        passwords = json.load(file)
        update_password_list()
except FileNotFoundError:
    pass


window.mainloop()
