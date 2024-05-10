import tkinter as tk
from tkinter import ttk, messagebox
import random

# Define Katakana and Hiragana characters
katakana_characters = {
    "ア": "a", "イ": "i", "ウ": "u", "エ": "e", "オ": "o",
    "カ": "ka", "キ": "ki", "ク": "ku", "ケ": "ke", "コ": "ko",
    "サ": "sa", "シ": "shi", "ス": "su", "セ": "se", "ソ": "so",
    "タ": "ta", "チ": "chi", "ツ": "tsu", "テ": "te", "ト": "to",
    "ナ": "na", "ニ": "ni", "ヌ": "nu", "ネ": "ne", "ノ": "no",
}

hiragana_characters = {
    "あ": "a", "い": "i", "う": "u", "え": "e", "お": "o",
    "か": "ka", "き": "ki", "く": "ku", "け": "ke", "こ": "ko",
    "さ": "sa", "し": "shi", "す": "su", "せ": "se", "そ": "so",
    "た": "ta", "ち": "chi", "つ": "tsu", "て": "te", "と": "to",
    "な": "na", "に": "ni", "ぬ": "nu", "ね": "ne", "の": "no",
}

# Define Kanji characters from Genki 1 to 12
kanji_genki = {
    "一": ("いち", "ひと"), "二": ("に", "ふた"), "三": ("さん", "み"), "四": ("し", "よん"), "五": ("ご", "いつ"),
    "六": ("ろく", "む"), "七": ("しち", "なな"), "八": ("はち", "や"), "九": ("きゅう", "ここの"), "十": ("じゅう", "とお"),
    "百": ("ひゃく", "もも"), "千": ("せん", "ち"), "万": ("まん", "よろず"), "円": ("えん", "まる"), "本": ("ほん", "もと"),
    "大": ("だい", "おお"), "小": ("しょう", "ちい"), "人": ("じん", "にん"), "学": ("がく", "まな"), "生": ("せい", "い"),
    "来": ("らい", "くる"), "年": ("ねん", "とし"), "時": ("じ", "とき"), "今": ("こん", "いま"), "毎": ("まい", "ごと"),
    "分": ("ぶん", "わ"), "間": ("かん", "あいだ"), "半": ("はん", "なか"), "何": ("なに", "なん"), "言": ("げん", "い"),
    "語": ("ご", "かた"), "行": ("こう", "い"), "飛": ("ひ", "と"), "食": ("しょく", "た"),
    "飲": ("いん", "の"), "見": ("けん", "み"), "買": ("ばい", "か"),
}

# Global variables
selected_characters = []
character = ""
correct_option = ""

# Function to start the game
def start_game():
    global character
    global correct_option

    # Check if any character sets are selected
    selected_sets = [k for k, v in zip(["Katakana", "Hiragana", "Kanji (Genki 1-12)"], set_vars) if v.get()]

    if not selected_sets:
        messagebox.showerror("Error", "Please select at least one character set!")
        return

    # Select a random character from the selected sets
    selected_characters = random.choice(selected_sets)

    # Display the selected character
    if selected_characters == "Katakana":
        character = random.choice(list(katakana_characters.keys()))
        correct_option = katakana_characters[character]
    elif selected_characters == "Hiragana":
        character = random.choice(list(hiragana_characters.keys()))
        correct_option = hiragana_characters[character]
    elif selected_characters == "Kanji (Genki 1-12)":
        character = random.choice(list(kanji_genki.keys()))
        correct_option = kanji_genki[character][0] if random.randint(0, 1) == 0 else kanji_genki[character][1]

    character_label.config(text=character)

    # Generate options
    generate_options()

# Function to generate options
def generate_options():
    # Clear previous options
    for button in option_buttons:
        button.grid_forget()

    # Randomize the order of options
    options = [correct_option]

    # For Kana characters, add a corresponding Kana option
    if selected_characters in ["Katakana", "Hiragana"]:
        options.append(random.choice(list(katakana_characters.values() if selected_characters == "Hiragana" else hiragana_characters.values())))

    # For Kanji characters, add both Onyomi and Kunyomi readings as options
    elif selected_characters == "Kanji (Genki 1-12)":
        onyomi, kunyomi = kanji_genki[character]
        options.append(onyomi if random.randint(0, 1) == 0 else kunyomi)

    # Randomly select one of the remaining Kana characters as the third option
    remaining_kana = [v for k, v in (katakana_characters if selected_characters == "Hiragana" else hiragana_characters).items() if v != correct_option]
    options.append(random.choice(remaining_kana))

    # Shuffle the options
    random.shuffle(options)

    # Update option buttons
    for i in range(len(options)):
        option_buttons[i].config(text=options[i])
        option_buttons[i].config(command=lambda option=options[i]: check_answer(option))
        option_buttons[i].grid(row=4, column=i, padx=10, pady=5)

# Function to check the answer
def check_answer(answer):
    if answer == correct_option:
        messagebox.showinfo("Correct", "Correct answer!")
        start_game()  # Automatically move to the next character
    else:
        messagebox.showerror("Incorrect", "Sorry, wrong answer! Please try again.")

# Create the main window
window = tk.Tk()
window.title("Japanese Character Learning Game")

# Create frame for character selection
character_frame = tk.Frame(window)
character_frame.grid(row=0, column=0, padx=10, pady=10)

# Create frame for options
options_frame = tk.Frame(window)
options_frame.grid(row=1, column=0, padx=10, pady=10)

# Character set selection
ttk.Label(character_frame, text="Select Character Sets:").grid(row=0, column=0, sticky="w")

set_vars = []
set_checkboxes = []

for i, (set_name, char_set) in enumerate([("Katakana", katakana_characters), ("Hiragana", hiragana_characters), ("Kanji (Genki 1-12)", kanji_genki)]):
    var = tk.IntVar()
    set_vars.append(var)
    checkbox = ttk.Checkbutton(character_frame, text=set_name, variable=var)
    checkbox.grid(row=i+1, column=0, sticky="w")
    set_checkboxes.append(checkbox)

# Character display
character_label = ttk.Label(window, text="", font=("Arial", 36))
character_label.grid(row=2, column=0, pady=20)

# Option buttons
option_buttons = [ttk.Button(options_frame, text="", width=10, command=lambda: None) for _ in range(3)]

# Start button
start_button = ttk.Button(window, text="Start", width=10, command=start_game)
start_button.grid(row=3, column=0, pady=10)

# Start the application
window.mainloop()
