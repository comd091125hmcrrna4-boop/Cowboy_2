import tkinter as tk
from tkinter import ttk, messagebox

class NoteApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Gestor de Notas")
        self.root.geometry("600x400")

        # Lista de notas (almacenadas en memoria)
        self.notes = []

        # --- Frame superior para entrada de texto ---
        frame_top = ttk.Frame(root, padding=10)
        frame_top.pack(fill="x")

        ttk.Label(frame_top, text="Escribe tu nota:").pack(anchor="w")
        self.text_note = tk.Text(frame_top, height=5, width=60)
        self.text_note.pack(fill="x")

        btn_save = ttk.Button(frame_top, text="Guardar Nota", command=self.save_note)
        btn_save.pack(pady=5, anchor="e")

        # --- Frame de búsqueda ---
        frame_search = ttk.Frame(root, padding=10)
        frame_search.pack(fill="x")

        ttk.Label(frame_search, text="Buscar nota:").pack(side="left")
        self.search_var = tk.StringVar()
        entry_search = ttk.Entry(frame_search, textvariable=self.search_var)
        entry_search.pack(side="left", fill="x", expand=True, padx=5)
        btn_search = ttk.Button(frame_search, text="Buscar", command=self.search_notes)
        btn_search.pack(side="left")

        # --- Frame lista de notas ---
        frame_list = ttk.Frame(root, padding=10)
        frame_list.pack(fill="both", expand=True)

        self.listbox = tk.Listbox(frame_list, height=10)
        self.listbox.pack(side="left", fill="both", expand=True)

        scrollbar = ttk.Scrollbar(frame_list, orient="vertical", command=self.listbox.yview)
        scrollbar.pack(side="right", fill="y")
        self.listbox.config(yscrollcommand=scrollbar.set)

        # --- Botones de acciones ---
        frame_actions = ttk.Frame(root, padding=10)
        frame_actions.pack(fill="x")

        btn_delete = ttk.Button(frame_actions, text="Borrar Nota", command=self.delete_note)
        btn_delete.pack(side="left", padx=5)

        btn_show = ttk.Button(frame_actions, text="Mostrar Nota", command=self.show_note)
        btn_show.pack(side="left", padx=5)

        btn_refresh = ttk.Button(frame_actions, text="Refrescar Lista", command=self.refresh_list)
        btn_refresh.pack(side="left", padx=5)

    def save_note(self):
        note = self.text_note.get("1.0", tk.END).strip()
        if note:
            self.notes.append(note)
            self.text_note.delete("1.0", tk.END)
            self.refresh_list()
            messagebox.showinfo("Éxito", "Nota guardada correctamente.")
        else:
            messagebox.showwarning("Aviso", "La nota está vacía.")

    def delete_note(self):
        selected = self.listbox.curselection()
        if selected:
            index = selected[0]
            del self.notes[index]
            self.refresh_list()
            messagebox.showinfo("Éxito", "Nota eliminada.")
        else:
            messagebox.showwarning("Aviso", "Selecciona una nota para borrar.")

    def show_note(self):
        selected = self.listbox.curselection()
        if selected:
            index = selected[0]
            messagebox.showinfo("Contenido de la nota", self.notes[index])
        else:
            messagebox.showwarning("Aviso", "Selecciona una nota para mostrar.")

    def search_notes(self):
        keyword = self.search_var.get().lower()
        self.listbox.delete(0, tk.END)
        for note in self.notes:
            if keyword in note.lower():
                self.listbox.insert(tk.END, note[:50] + ("..." if len(note) > 50 else ""))

    def refresh_list(self):
        self.listbox.delete(0, tk.END)
        for note in self.notes:
            self.listbox.insert(tk.END, note[:50] + ("..." if len(note) > 50 else ""))

if __name__ == "__main__":
    root = tk.Tk()
    app = NoteApp(root)
    root.mainloop()
