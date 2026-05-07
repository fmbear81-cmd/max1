# max1
# GitHub User Finder

**Автор:** Максим Фефелов

## Описание

GUI-приложение для поиска пользователей GitHub и добавления их в избранное. Информация об избранных сохраняется в файл favorites.json.

## Как использовать

1. Введите логин пользователя GitHub в поле.
2. Нажмите «Поиск» для отображения результатов.
3. Выберите пользователя и нажмите «Добавить в избранное».
4. Для сохранения избранных нажмите кнопку «Сохранить избранное».

## API-инструкция

- Используется публичный API: `https://api.github.com/users/{username}`
- Запрос возвращает JSON с данными о пользователе.

## Пример использования:
import tkinter as tk
from tkinter import messagebox
import requests
import json
import os

# Основное окно
root = tk.Tk()
root.title("GitHub User Finder")

# Глобальный список избранных
favorites = []

# Функция для поиска пользователя
def search_user():
    username = entry.get().strip()
    if not username:
        messagebox.showwarning("Ошибка", "Поле поиска не должно быть пустым.")
        return
    
    url = f"https://api.github.com/users/{username}"
    response = requests.get(url)
    if response.status_code == 200:
        data = response.json()
        display_user(data)
    else:
        messagebox.showinfo("Результат", "Пользователь не найден.")

# Отображение результата поиска
def display_user(user_data):
    user_list.delete(0, tk.END)
    user_list.insert(tk.END, user_data['login'])

# Добавление пользователя в избранное
def add_to_favorites():
    selected = user_list.curselection()
    if not selected:
        messagebox.showwarning("Внимание", "Выберите пользователя из списка.")
        return
    user = user_list.get(selected[0])
    if user not in favorites:
        favorites.append(user)
        fav_list.insert(tk.END, user)
    else:
        messagebox.showinfo("Информация", "Пользователь уже в избранном.")

# Сохранение избранных в JSON
def save_favorites():
    with open("favorites.json", "w", encoding="utf-8") as f:
        json.dump(favorites, f)
    messagebox.showinfo("Готово", "Избранное сохранено в favorites.json.")

# Загрузка избранных при запуске
def load_favorites():
    if os.path.exists("favorites.json"):
        with open("favorites.json", "r", encoding="utf-8") as f:
            loaded = json.load(f)
            favorites.extend(loaded)
            for user in loaded:
                fav_list.insert(tk.END, user)

# Интерфейс
# Поле поиска
label = tk.Label(root, text="Введите логин GitHub:")
label.pack(pady=5)

entry = tk.Entry(root, width=40)
entry.pack(pady=5)

# Кнопка поиска
search_btn = tk.Button(root, text="Поиск", command=search_user)
search_btn.pack(pady=5)

# Результаты поиска
tk.Label(root, text="Результаты поиска:").pack(pady=5)
user_list = tk.Listbox(root, height=5, width=50)
user_list.pack(pady=5)

# Добавить в избранное
add_fav_btn = tk.Button(root, text="Добавить в избранное", command=add_to_favorites)
add_fav_btn.pack(pady=5)

# Избранное
tk.Label(root, text="Избранное:").pack(pady=5)
fav_list = tk.Listbox(root, height=5, width=50)
fav_list.pack(pady=5)

# Сохранить избранное
save_btn = tk.Button(root, text="Сохранить избранное", command=save_favorites)
save_btn.pack(pady=10)

# Загрузка избранных при запуске
load_favorites()

root.mainloop()
