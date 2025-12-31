#include <iostream>
#include <conio.h>
#include <windows.h>
using namespace std;
const int width = 20;
const int height = 20;
// Direction enum
enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };
eDirection dir;
 
// Game state
bool gameOver;

// Snake structure
struct Snake {
    int x, y;
};

// Food structure
struct Food {
    int x, y;
};
 
Snake snake[100];
Food food;
int snakeLength;
int score;
 
// Setup function
void Setup() {
    gameOver = false;
    dir = STOP;
    snakeLength = 1;
    snake[0].x = width / 2;
    snake[0].y = height / 2;
    food.x = rand() % (width - 2) + 1;
    food.y = rand() % (height - 2) + 1;
    score = 0;
}
 
// Draw game
void Draw() {
    system("cls");
 
    for (int i = 0; i < width + 2; i++)
        cout << "*";
    cout << endl;
 
    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {
            if (j == 0)
                cout << "*";
 
            if (i == snake[0].y && j == snake[0].x)
                cout << "H"; // head
            else if (i == food.y && j == food.x)
                cout << "0"; // food
            else {
                bool printTail = false;
                for (int k = 1; k < snakeLength; k++) {
                    if (snake[k].x == j && snake[k].y == i) {
                        cout << "o";
                        printTail = true;
                        break;
                    }
                }
                if (!printTail)
                    cout << " ";
            }
 
            if (j == width - 1)
                cout << "*";
        }
        cout << endl;
    }
 
    for (int i = 0; i < width + 2; i++)
        cout << "*";
    cout << "\nScore: " << score << endl;
}
 
// Input
void Input() {
    if (_kbhit()) {
        switch (_getch()) {
        case 'a': dir = LEFT; break;
        case 'd': dir = RIGHT; break;
        case 'w': dir = UP; break;
        case 's': dir = DOWN; break;
        case 'x': gameOver = true; break;
        }
    }
}
 
// Game logic
void Logic() {
    // Move tail
    for (int i = snakeLength - 1; i > 0; i--) {
        snake[i] = snake[i - 1];
    }
 
    // Move head
    switch (dir) {
    case LEFT:  snake[0].x--; break;
    case RIGHT: snake[0].x++; break;
    case UP:    snake[0].y--; break;
    case DOWN:  snake[0].y++; break;
    default: break;
    }
 
    // Wall collision
    if (snake[0].x < 0 || snake[0].x >= width ||
        snake[0].y < 0 || snake[0].y >= height) {
        gameOver = true;
    }
 
    // Self collision
    for (int i = 1; i < snakeLength; i++) {
        if (snake[0].x == snake[i].x && snake[0].y == snake[i].y) {
            gameOver = true;
        }
    }
 
    // Food collision
    if (snake[0].x == food.x && snake[0].y == food.y) {
        score += 10;
        food.x = rand() % (width - 2) + 1;
        food.y = rand() % (height - 2) + 1;
        snakeLength++;
    }
}
 
int main() {
    Setup();
    while (!gameOver) {
        Draw();
        Input();
        Logic();
        Sleep(100); // speed
    }
 
    system("cls");
    cout << "Game Over!" << endl;
    cout << "Final Score: " << score << endl;
    return 0;
}
