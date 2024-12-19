import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.Random;
import javax.swing.*;

abstract class Game {
    protected String secretCombination; 
    protected abstract void generateSecretCombination();

    protected boolean isValidGuess(String guess) {
        return guess.matches("[RGBYOP]{4}"); 
    }

    public void startNewGame() {
        generateSecretCombination();
    }
}

class Mastermind extends Game {
    private static final char[] COLORS = {'R', 'G', 'B', 'Y', 'O', 'P'}; 

    @Override
    protected void generateSecretCombination() {
        Random random = new Random();
        StringBuilder sb = new StringBuilder();
        while (sb.length() < 4) {
            char color = COLORS[random.nextInt(COLORS.length)]; 
            if (sb.toString().indexOf(color) == -1) { 
                sb.append(color);
            }
        }
        secretCombination = sb.toString();
        System.out.println("Secret Combination (debug): " + secretCombination); 
    }

    public String checkGuess(String guess) {
        int correctPosition = 0; 
        int correctColor = 0;  

        for (int i = 0; i < 4; i++) {
            if (guess.charAt(i) == secretCombination.charAt(i)) {
                correctPosition++;
            } else if (secretCombination.indexOf(guess.charAt(i)) != -1) {
                correctColor++;
            }
        }

        if (correctPosition == 4) {
            return "Congratulations! You guessed the combination: " + secretCombination;
        }
        return "Correct Position: " + correctPosition + ", Correct Colors: " + correctColor;
    }
}

public class MastermindGame extends JFrame {
    private Mastermind mastermind; 
    private JTextField guessField; 
    private JTextArea resultArea;  
    private JButton guessButton, newGameButton; 

    public MastermindGame() {
        mastermind = new Mastermind(); 
        mastermind.startNewGame();   

        setTitle("Mastermind Game - Colors Edition");
        setSize(400, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        JPanel inputPanel = new JPanel(new FlowLayout());
        inputPanel.setBackground(new Color(173, 216, 230)); 
        JLabel guessLabel = new JLabel("Enter your guess (R, G, B, Y, O, P): ");
        guessField = new JTextField(10);
        guessButton = new JButton("Guess");
        guessButton.setBackground(new Color(144, 238, 144)); 
        inputPanel.add(guessLabel);
        inputPanel.add(guessField);
        inputPanel.add(guessButton);

        resultArea = new JTextArea();
        resultArea.setEditable(false);
        resultArea.setBackground(new Color(255, 250, 205)); 
        resultArea.setForeground(Color.DARK_GRAY); 
        JScrollPane scrollPane = new JScrollPane(resultArea);

        JPanel bottomPanel = new JPanel();
        bottomPanel.setBackground(new Color(255, 182, 193));
        newGameButton = new JButton("New Game");
        newGameButton.setBackground(new Color(255, 105, 180));
        bottomPanel.add(newGameButton);

        add(inputPanel, BorderLayout.NORTH);
        add(scrollPane, BorderLayout.CENTER);
        add(bottomPanel, BorderLayout.SOUTH);

    
        guessButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                handleGuess();
            }
        });

        newGameButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                mastermind.startNewGame();
                resultArea.setText("");
                guessField.setText("");
                guessButton.setEnabled(true);
            }
        });
    }
    private void handleGuess() {
        String guess = guessField.getText().toUpperCase(); 
        if (!mastermind.isValidGuess(guess)) {  
            JOptionPane.showMessageDialog(this, "Please enter a valid combination of 4 colors (R, G, B, Y, O, P)!");
            return;
        }

        String result = mastermind.checkGuess(guess);
        resultArea.append("Guess: " + guess + " | " + result + "\n");

        if (result.contains("Congratulations")) { 
            guessButton.setEnabled(false);
            JOptionPane.showMessageDialog(this, result);
        }

        guessField.setText("");}

    
public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            MastermindGame game = new MastermindGame();
            game.setVisible(true);
        });
    }
}
