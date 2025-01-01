from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button

class GuessingGameApp(App):
    def build(self):
        self.secret_number1 = ""
        self.secret_number2 = ""
        self.turn = 1  # 1 for Player 1, 2 for Player 2

        self.layout = BoxLayout(orientation='vertical', padding=10, spacing=10)

        self.label = Label(text="Player 1: Enter your secret number:")
        self.layout.add_widget(self.label)

        self.text_input = TextInput(multiline=False, password=True)
        self.layout.add_widget(self.text_input)

        self.submit_button = Button(text="Submit")
        self.submit_button.bind(on_press=self.submit_secret)
        self.layout.add_widget(self.submit_button)

        self.result_label = Label(text="")
        self.layout.add_widget(self.result_label)

        return self.layout

    def submit_secret(self, instance):
        input_text = self.text_input.text.strip()

        if not input_text.isdigit() or len(input_text) != 4 or len(set(input_text)) != 4:
            self.result_label.text = "Please enter a valid 4-digit number without repetition."
            return

        if self.turn == 1:
            self.secret_number1 = input_text
            self.label.text = "Player 2: Enter your secret number:"
            self.result_label.text = ""
            self.text_input.text = ""
            self.turn = 2
        elif self.turn == 2:
            self.secret_number2 = input_text
            self.label.text = "Player 1: Guess Player 2's number:"
            self.result_label.text = ""
            self.text_input.text = ""
            self.turn = 3
        elif self.turn == 3:
            self.check_guess(self.secret_number2, input_text, "Player 1", "Player 2")
        elif self.turn == 4:
            self.check_guess(self.secret_number1, input_text, "Player 2", "Player 1")

    def check_guess(self, secret, guess, current_player, opponent):
        correct_place = sum(1 for a, b in zip(secret, guess) if a == b)
        correct_number = sum(1 for char in guess if char in secret) - correct_place

        if correct_place == 4:
            self.result_label.text = f"{current_player} wins!"
            self.label.text = "Game Over"
            self.text_input.disabled = True
            self.submit_button.disabled = True
        else:
            self.result_label.text = f"Correct places: {correct_place}, Correct numbers: {correct_number}"
            if current_player == "Player 1":
                self.label.text = "Player 2: Guess Player 1's number:"
                self.turn = 4
            else:
                self.label.text = "Player 1: Guess Player 2's number:"
                self.turn = 3
        self.text_input.text = ""

if __name__ == "__main__":
    GuessingGameApp().run()
