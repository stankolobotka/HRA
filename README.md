# HRA
#include <iostream>
#include <stdlib.h>
#include <windows.h>
#include <conio.h>
#include <ctime>
using namespace std;


bool gameOver;
bool invalidCoord;
const int width = 20;
const int height = 20;
int x, y, fruitX, fruitY, score;
int tailX[100], tailY[100];
int tailLength;

enum Direction { STOP = 0, LEFT, RIGHT, UP, DOWN};
Direction dir;

void ClearScreen()
{   
   
    COORD cursorPosition;   cursorPosition.X = 0;   cursorPosition.Y = 0;   SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), cursorPosition);
}

void Setup() 
{   // Initialise variables
    gameOver = false;
    dir = STOP;
    srand(time(0));
    x = width / 2;
    y = height / 2;
    fruitX = rand() % width;
    fruitY = rand() % height;
    score = 0;

}

void Draw() 
    ClearScreen();

    // Draws top border
    for (int i = 0; i < width + 2; i++)
        cout << '-';
    cout << endl;

    for (int i = 0; i < height; i++)
    {
        for (int k = 0; k < width; k++)
        {   
            // Left border
            if (k == 0)
                cout << '|';
            // Snake's head
            if (i == y && k == x)
                cout << '@';
            // Fruit
            else if (i == fruitY && k == fruitX)
                cout << '*';

            else
            {   
                 
                bool printTail = false;
                for (int j = 0; j < tailLength; j++)
                {
                    if (tailX[j] == k && tailY[j] == i)
                    {
                        cout << 'o';
                        printTail = true;
                    }
                }
               
                if (!printTail)
                    cout << ' ';
            }

           
            if (k == width - 1)
                cout << '|';

        }
        cout << endl;
    }

    
    for (int i = 0; i < width + 2; i++)
        cout << '-';
    cout << endl;

    
    cout << endl;
    cout << "Score: " << score << endl;

}
void Input()
{   
    
    if (_kbhit())
    {
        switch (_getch())
        {
        case 'w':
            if (dir != DOWN)
                dir = UP;
            break;
        case 'a':
            if (dir != RIGHT)
                dir = LEFT;
            break;
        case 's':
            if (dir != UP)
                dir = DOWN;
            break;
        case 'd':
            if (dir != LEFT)
                dir = RIGHT;
            break;
        case 'k':
            gameOver = true;
            break;
        }

    }
}

void Logic() 
{   
    
    int prevX = tailX[0];
    int prevY = tailY[0];
    int prevX2, prevY2;
    tailX[0] = x;
    tailY[0] = y;

    for (int i = 1; i < tailLength; i++)
    {
        prevX2 = tailX[i];
        prevY2 = tailY[i];
        tailX[i] = prevX;
        tailY[i] = prevY;
        prevX = prevX2;
        prevY = prevY2;
    }
   
    switch (dir)
    {
    case LEFT:
        x--;
        break;
    case RIGHT:
        x++;
        break;
    case UP:
        y--;
        break;
    case DOWN:
        y++;
        break;
    }

    
    for (int i = 0; i < tailLength; i++)
        if (tailX[i] == x && tailY[i] == y)
            gameOver = true;

   
    if (x == fruitX && y == fruitY)
    {
        score += 1;
        fruitX = rand() % width;
        fruitY = rand() % height;
        
        for (int i = 0; i < tailLength; )
        {   
            invalidCoord = false;
            if (tailX[i] == fruitX && tailY[i] == fruitY) {
                invalidCoord = true;
                fruitX = rand() % width;
                fruitY = rand() % height;
                break;
            }
            if (!invalidCoord)
                i++;
        }
        tailLength++;
    }

   
    if (y >= height)
        y = 0;
    else if (y < 0)
        y = height - 1;
    if (x >= width)
        x = 0;
    else if (x < 0)
        x = width - 1;
}

int main()
{
    Setup();
    while (!gameOver) // Game mainloop 
    {
        Draw();
        if (dir == UP || DOWN)
            Sleep(25); // Helps to equate vertical snake movement speed and horizontal speed
        Sleep(40);
        Input();
        Logic();

    }

    return 0;
}
