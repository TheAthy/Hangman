/*
 * File: Hangman.java
 * ------------------
 * This program will eventually play the Hangman game from
 * Assignment #4.
 */

import acm.graphics.*;
import acm.program.*;
import acm.util.*;

import java.awt.*;

public class Hangman extends ConsoleProgram {
	public void init() {
		canvas = new HangmanCanvas();
		add(canvas);
	}

	private HangmanCanvas canvas;
	// number of tries
	private int guessCounter = 8;
	private char ch;
	// secret word
	private String hiddenWord;
	private HangmanLexicon words;
	private RandomGenerator rgen = RandomGenerator.getInstance();
	private String wrongLetters = "";

	// chooses a random word from hangmanLexicon
	private String choose() {
		words = new HangmanLexicon();
		int Random = rgen.nextInt(0, (words.getWordCount() - 1));
		String picked = words.getWord(Random);
		return picked;
	}

	private String word = choose();

	// shows a number of letters
	private String lettersNumber() {
		String result = "";
		for (int i = 0; i < word.length(); i++) {
			result = result + "-";
		}
		return result;
	}

	// checks if chosen character is in the hidden word
	private void letterCheck() {
		for (int i = 0; i < word.length(); i++) {
			if (ch == word.charAt(i)) {
				if (i == 0) {
					hiddenWord = ch + hiddenWord.substring(1);
				}
				if (i > 0) {
					hiddenWord = hiddenWord.substring(0, i) + ch + hiddenWord.substring(i + 1);
				}
				canvas.displayWord(hiddenWord);
			}
		}
		if (word.indexOf(ch) == -1) {
			println("There are no " + ch + "'s in the word");
			guessCounter--;
			wrongLetters = wrongLetters + ch;
			canvas.noteIncorrectGuess(wrongLetters);
		}
		if (word.indexOf(ch) != -1) {
			println("Guess is correct.");
		}
	}

	private void createGame() {
		canvas.reset();
		hiddenWord = lettersNumber();
		canvas.displayWord(hiddenWord);
	}

	private void playGame() {
		while (guessCounter > 0) {
			String getChar = readLine("your guess: ");
			while (true) {
				// if user writes more than 1 character
				if (getChar.length() > 1) {
					getChar = readLine("you can guess only 1 word at one try.");
				}
				// if user doesn't write any character
				if (getChar.length() < 1) {
					println("you have to write at least 1 character.");
					getChar = readLine("your guess: ");
				}
				// if user writes 1 character
				if (getChar.length() == 1) {
					break;
				}
			}
			ch = getChar.charAt(0);
			// if user write lower case character, it changes to upper case
			if (Character.isLowerCase(ch)) {
				ch = Character.toUpperCase(ch);
			}
			letterCheck();
			// if use guesses the word
			if (hiddenWord.equals(word)) {
				println("you guessed a word: " + word);
				println("you win.");
				break;
			}
			println("the word now looks like this " + hiddenWord);
			println("you have " + guessCounter + " guesses left.");
		}
		// if user didn't guess the word and he is out of tries
		if (guessCounter == 0) {
			println("you're completely hung.");
			println("the word was " + word);
			println("you lose.");
		}
	}

	public void run() {
		createGame();
		playGame();
	}

}
