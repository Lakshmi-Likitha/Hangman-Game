# Hangman-Game
Hangman is a word-guessing game that involves two players: one player thinks of a secret word, and the other player tries to guess the word by suggesting individual letters within a certain number of attempts. 

<h2>Description:</h2>
This repository contains a Python implementation of the classic Hangman game. The game employs a simple user interface for guessing letters and tries to guess a secret word intelligently using a predefined word list. The AI's guessing strategy is based on analyzing word patterns and frequency analysis of letters.

<h2>Features:</h2>
User-friendly text-based interface for playing the Hangman game.
AI-driven secret word selection and letter guessing.
The AI employs frequency analysis and pattern matching for intelligent guessing.
The game ends when the word is guessed correctly or when the hangman figure is completed.

<h2>How to run</h2>
Make sure you have Python installed on your system.<br>
Download the <a href="NewHangman.ipynb">NewHangman</a> notebook from this repository.<br>
Open the notebook using Jupyter Notebook or Jupyter Lab.<br>
Execute the notebook cells to run the Hangman game.<br>
To execute a cell in the notebook, you can use the "Run" button or press Shift + Enter while the cell is selected. Follow the prompts to interact with the game and make your guesses.

<h3>Import Statements</h3>


    import random
    import re
    import collections
    import nltk
    from nltk.corpus import words
    nltk.download('words')

The code imports necessary modules like random, re, collections, and nltk for various functionalities.

Please note that the game uses the NLTK library, which might need to be installed separately if you don't have it already. You can install it using the following command:


    pip install nltk

  
The program will display an underscore for each letter in the secret word and prompt you to guess a letter.

Enter a single letter and press Enter. The program will inform you if the guessed letter is correct or incorrect.

The game continues until you guess the entire word correctly or the hangman figure is completed.

You win if you guess the word before the hangman figure is drawn completely. Otherwise, you lose.

<h2>AI Strategy:</h2>
The AI intelligently guesses letters based on word patterns and the frequency of letters in common English words. It aims to maximize its chances of guessing     the word correctly by considering potential patterns and n-grams (groups of consecutive letters) in the secret word.

<h2>APPROACH: N-grams</h2>
I have implemented n-grams approach for the given Hangman Challenge which extracts a sequence of n consecutive items (usually words or characters) from a given   text. N-grams are used to capture the local relationships between adjacent elements in a sequence. 
Here I have used 2,3,4 and 5 grams to predict the word.

<h3>__init__()</h3>


    def __init__(self):
        self.guessed_letters = []
        self.successful_games = 0
        self.total_games = 0
        self.word_list = set(words.words())
        self.max_lives = 6

        full_dictionary_location = "words_250000_train.txt"
        self.full_dictionary = self.build_dictionary(full_dictionary_location) 
        self.excluded_words = self.build_excluded_words_set(full_dictionary_location)  # Use the correct attribute name here
        self.full_dictionary_common_letter_sorted = collections.Counter("".join(self.full_dictionary)).most_common()
        self.ngram_2, self.ngram_3, self.ngram_4, self.ngram_5 = self.build_ngram_dicts()

        self.current_dictionary = []
The __init__ method initializes the HangmanGame class. It sets up attributes for guessed letters, successful games, total games, a set of English words, maximum lives, and more. It also reads a full dictionary from a text file and builds various n-gram dictionaries based on word frequency. self.current_dictionary is set to an empty list.

<h3>build_excluded_words_set()</h3>


    def build_excluded_words_set(self, full_dictionary_location):
        with open(full_dictionary_location, "r") as text_file:
            excluded_words = set(text_file.read().splitlines())
        return excluded_words
The build_excluded_words_set method reads a text file containing excluded words and returns a set of these words.

<h3>generate_random_wods()</h3>


    def generate_random_word(self):
        word_list = words.words()
        return random.choice(word_list)
The generate_random_word method selects a random word from the NLTK words corpus.
  
<h3>build_ngram_dicts()</h3> 


    def build_ngram_dicts(self):
        ngram_2 = collections.defaultdict(int)
        ngram_3 = collections.defaultdict(int)
        ngram_4 = collections.defaultdict(int)
        ngram_5 = collections.defaultdict(int)
        
        for word in self.full_dictionary:
            if not self.is_valid_word(word):
                continue
            # Build 2-gram dict
            for i in range(len(word)-1):
                ngram_2[word[i:i+2]] += 1
                
            # Build 3-gram dict
            for i in range(len(word)-2):
                ngram_3[word[i:i+3]] += 1
            
            # Build 4-gram dict
            for i in range(len(word)-3):
                ngram_4[word[i:i+4]] += 1

            # Build 5-gram dict
            for i in range(len(word)-4):
                ngram_5[word[i:i+5]] += 1
        
        return dict(ngram_2), dict(ngram_3), dict(ngram_4), dict(ngram_5)
•	build_ngram_dicts creates dictionaries for different n-gram lengths (2, 3, 4, 5). It loops through each word in the self.full_dictionary and checks if the word   is valid using the is_valid_word method.

•	For each valid word, it loops through the characters and builds n-grams of lengths 2 to 5. It increments the count of each n-gram in the respective dictionary    as ngram_2, ngram_3.

•	At the end, it returns dictionaries for each n-gram length containing the n-gram frequencies.

<h3>is_valid_word()</h3>


    def is_valid_word(self, word, threshold=3):
        count = 1
        prev_char = word[0]
        for char in word[1:]:
            if char == prev_char:
                count += 1
                if count > threshold:
                    return False
            else:
                count = 1
            prev_char = char
        return True
•	is_valid_word checks if a word is valid for the Hangman game. It counts the number of contiguous characters and checks if the count exceeds the given threshold   (default is 3). If it does, the word is considered invalid due to too many repeated characters.

This threshold is used to determine if a character sequence like 'aaa' or 'lll' occurs excessively in a word. If it does, the word is considered invalid.

<h3>generate_patterns()</h3>


    def generate_patterns(self, word, n):
        if n == 2:
            return [word[i:i+2] for i in range(len(word)-1) if "_" in word[i:i+2] and word[i:i+2] != "__"]
        elif n == 3:
            return [word[i:i+3] for i in range(len(word)-2) if word[i:i+3].count("_") == 1]
        elif n == 4:
            return [word[i:i+4] for i in range(len(word)-3) if word[i:i+4].count("_") == 1]
        elif n == 5:
            return [word[i:i+5] for i in range(len(word)-3) if word[i:i+5].count("_") == 1]
        else:
            return []
•	This method generates patterns based on the provided clean_word and n (n can be 2, 3, 4, or 5). A pattern is a sequence of characters in clean_word where       
underscores (_) indicate positions that need to be guessed. The method generates patterns for n-grams based on these underscores.

•	If n is 2:
It generates pairs of characters within the word using a sliding window approach.
For example, if word is "c_a_r", it generates "a", "a", but not "__" (no pattern).
These patterns are useful when there is an underscore between two known letters.

•	If n is 3, 4, or 5:
It generates longer patterns of 3, 4, or 5 characters respectively, again using a sliding window.
It checks if these patterns have exactly one underscore (_) in them using the count method.
For example, if word is "c__r_", it generates "ab" (c_a_r), "b_c_", "c__r", but not "a" (two underscores).
These patterns are useful when one known letter is followed by an unknown letter.

•	If n is anything other than 2, 3, 4, or 5:
It returns an empty list, as these values of n are not supported.

The generate_patterns() method helps to identify positions in the word where a known letter is adjacent to an unknown letter (underscore), or where an unknown  letter separates two known letters. These patterns provide hints for making better guesses in the Hangman game.
  
<h3>guess()</h3>


    def guess(self, word):
        clean_word = word[::2]
        
        letter_counts = {}

        # 5-grams with weight 4
        for pattern in self.generate_patterns(clean_word, 5):
            print(f"Generated 5-gram pattern: {pattern}")
            for ngram, count in self.ngram_5.items():
                pattern_regex = re.compile(pattern.replace("_", "."))
                if pattern_regex.match(ngram):
                    letter = ngram[pattern.index("_")]
                    letter_counts[letter] = letter_counts.get(letter, 0) + count * 5
    
        # 4-grams with weight 3
        for pattern in self.generate_patterns(clean_word, 4):
            print(f"Generated 4-gram pattern: {pattern}")
            for ngram, count in self.ngram_4.items():
                pattern_regex = re.compile(pattern.replace("_", "."))
                if pattern_regex.match(ngram):
                    letter = ngram[pattern.index("_")]
                    letter_counts[letter] = letter_counts.get(letter, 0) + count * 4
        
        # 3-grams with weight 2
        for pattern in self.generate_patterns(clean_word, 3):
            print(f"Generated 3-gram pattern: {pattern}")
            for ngram, count in self.ngram_3.items():
                pattern_regex = re.compile(pattern.replace("_", "."))
                if pattern_regex.match(ngram):
                    letter = ngram[pattern.index("_")]
                    letter_counts[letter] = letter_counts.get(letter, 0) + count * 3
        
        # 2-grams with weight 1
        for pattern in self.generate_patterns(clean_word, 2):
            print(f"Generated 2-gram pattern: {pattern}")
            for ngram, count in self.ngram_2.items():
                pattern_regex = re.compile(pattern.replace("_", "."))
                if pattern_regex.match(ngram):
                    letter = ngram[pattern.index("_")]
                    letter_counts[letter] = letter_counts.get(letter, 0) + count

        if not letter_counts:
            for letter, _ in self.full_dictionary_common_letter_sorted:
                if letter not in self.guessed_letters:
                    return letter    
            return random.choice('abcdefghijklmnopqrstuvwxyz')

        unguessed_letter_counts = {k: v for k, v in letter_counts.items() if k not in self.guessed_letters}
        return max(unguessed_letter_counts, key=unguessed_letter_counts.get, default="_")
•	guess method initializes the necessary variables. clean_word is a version of the given word without underscores (to consider only the known letters). len_word    stores the length of clean_word. letter_counts will store the frequency-weighted counts of letters based on n-grams.

•	Loop which iterates through the patterns generated by the generate_patterns method for n = 5. For each pattern, it iterates through the 5-grams stored in         self.ngram_5 (generated earlier) and checks if the pattern matches any of the 5-grams. If the pattern matches, it extracts the letter at the underscore's           position, and updates letter_counts with the weighted count based on the 5-gram's frequency.

•	The subsequent blocks do the same for patterns of lengths 4, 3, and 2, but with different weights (count * 4, count * 3, and count respectively) to reflect       their importance in guessing the next letter.

•	If no letter counts were found, the method returns the most common letter in the dictionary that hasn't been guessed yet. If that's not possible, it returns 
 a    random letter from the alphabet.

•	If there are letter counts, the method filters out letters that have already been guessed and returns the letter with the highest weighted count, using the max   function with a custom key function. If no eligible letter is found, it returns an underscore (_), indicating no valid letter guess.

•	Overall, the guess method uses n-gram patterns and their frequencies to make educated guesses for the next letter in the Hangman game, considering both known   
and unknown letters in the word.

<h3>build_dictionary()</h3>


    def build_dictionary(self, dictionary_file_location):
            with open(dictionary_file_location, "r") as text_file:
                full_dictionary = text_file.read().splitlines()
            return full_dictionary
The build_dictionary method reads a text file containing a full dictionary of words and returns a list of these words.

<h3>display_word()</h3>
    

    def display_word(self, word, guessed_letters):
         displayed_word = ""
         for letter in word:
             if letter in guessed_letters:
                 displayed_word += letter + " "
             else:
                 displayed_word += "_ "
         return displayed_word.strip()
The display_word method generates a string representation of the word, revealing guessed letters and using underscores for missing letters.

<h3>start_game()</h3>
    

    def start_game(self, word=None, verbose=True):
        
        self.guessed_letters = []
        
        self.current_dictionary = self.full_dictionary

        self.total_games += 1

        # Filter the word_list to exclude words in the excluded_words set
        valid_words = [w for w in self.word_list if w not in self.excluded_words]

        if word is None or word not in valid_words:
            print("Choosing a random word from valid words list.")
            if not valid_words:
                print("No valid words available.")
                return False
            word = random.choice(valid_words)

        if word in self.excluded_words:
            print(f"The provided word '{word}' is excluded.")
            return False

        lives = self.max_lives
        guessed_word = ["_"] * len(word)

        print("Word:", self.display_word(word, self.guessed_letters))

        while lives > 0:
            guess_letter = self.guess(" ".join(guessed_word))
            self.guessed_letters.append(guess_letter)

            # Check if the guessed letter is in the word
            if guess_letter in word:
                for i, letter in enumerate(word):
                    if letter == guess_letter:
                        guessed_word[i] = guess_letter
                if "_" not in guessed_word:
                    self.successful_games += 1  # Increment successful games count
                    print("Word:", self.display_word(word, self.guessed_letters))
                    print("Congratulations! You've guessed the word!")
                    return True
            else:
                lives -= 1
                print("Incorrect guess. Lives remaining:", lives)
                print("Guessed letters:", " ".join(self.guessed_letters))

            print("Word:", self.display_word(word, self.guessed_letters))

        print("Out of lives. The word was:", word)
        return False
The start_game method initiates a Hangman game, choosing a random word if necessary and allowing the player to guess letters until the game ends.


<h2>This Approach is 40% accurate and still under development.</h2>
