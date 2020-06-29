#include<stdio.h>
#include<conio.h>
#include<time.h>
#include<Windows.h>

#define consoleWidth 50
#define consoleHeight 25

void TextColor(int color)
{
	SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}

void clrscr(void)
{
	CONSOLE_SCREEN_BUFFER_INFO csbiInfo;
	HANDLE hConsoleOut;
	COORD Home = { 0,0 };
	DWORD dummy;

	hConsoleOut = GetStdHandle(STD_OUTPUT_HANDLE);
	GetConsoleScreenBufferInfo(hConsoleOut, &csbiInfo);

	FillConsoleOutputCharacter(hConsoleOut, ' ', csbiInfo.dwSize.X * csbiInfo.dwSize.Y, Home, &dummy);
	csbiInfo.dwCursorPosition.X = 0;
	csbiInfo.dwCursorPosition.Y = 0;
	SetConsoleCursorPosition(hConsoleOut, csbiInfo.dwCursorPosition);
}
void gotoXY(short x, short y)
{
	HANDLE hConsoleOutput;
	COORD Cursor_an_Pos = { x,y };
	hConsoleOutput = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleCursorPosition(hConsoleOutput, Cursor_an_Pos);
}

using namespace std;
enum TrangThai { up, down, left, right };

struct point
{
	int x, y;
};

struct Snake
{
	point a[100];
	int n;
	TrangThai huong;
};

struct food
{
	point vitri;
};

void KhoiTao(Snake& snake, food& food)
{
	snake.n = 1;
	snake.a[0].x = 0;
	snake.a[0].y = 0;
	snake.huong = down;
	food.vitri.x = 7;
	food.vitri.y = 7;
}


void HienThi(Snake snake, food food)
{
	clrscr();
	TextColor(7);
	for (int i = 0; i < consoleHeight; i++)
	{
		gotoXY(consoleWidth, i);
		putchar(179);
	}
	TextColor(2);
	gotoXY(food.vitri.x, food.vitri.y);
	putchar('@');
	TextColor(13);
	gotoXY(snake.a[0].x, snake.a[0].y);
	putchar(2);
	for (int i = 1; i < snake.n; i++)
	{
		gotoXY(snake.a[i].x, snake.a[i].y);
		putchar('O');
	}
}

void DieuKhien(Snake& snake)
{
	//trang thai cua tung dot
	for (int i = snake.n - 1; i > 0; i--)
		snake.a[i] = snake.a[i - 1];

	if (_kbhit())
	{
		int key = _getch();
		//dieu khien cai dau snake
		if (key == 'a' || key == 'A')
			snake.huong = left;
		else
			if (key == 'w' || key == 'W')
				snake.huong = up;
			else
				if (key == 'S' || key == 's')
					snake.huong = down;
				else
					if (key == 'd' || key == 'D')
						snake.huong = right;
	}
	if (snake.huong == up)
		snake.a[0].y--;
	else if (snake.huong == down)
		snake.a[0].y++;
	else if (snake.huong == left)
		snake.a[0].x--;
	else if (snake.huong == right)
		snake.a[0].x++;
}
//
int Xuly(Snake& snake, food& food, int& thoigian)
{
	if (snake.a[0].x < 0 || snake.a[0].x >= consoleWidth ||
		snake.a[0].y < 0 || snake.a[0].y >= consoleHeight)
		return -1;

	for (int i = 1; i < snake.n; i++)
		if (snake.a[0].x == snake.a[i].x && snake.a[0].y == snake.a[i].y)
			return -1;

	if (snake.a[0].x == food.vitri.x && snake.a[0].y == food.vitri.y)
	{
		for (int i = snake.n; i > 0; i--)
			snake.a[i] = snake.a[i - 1];
		snake.n++;
		if (snake.huong == up)
			snake.a[0].y--;
		else if (snake.huong == down)
			snake.a[0].y++;
		else if (snake.huong == left)
			snake.a[0].x--;
		else if (snake.huong == right)
			snake.a[0].x++;

		food.vitri.x = rand() % consoleWidth;
		food.vitri.y = rand() % consoleHeight;
		if (thoigian > 50)
			thoigian -= 5;
	}

}

int main()
{
	int ma;
	int thoigian = 200;
	srand(time(NULL)); // khoi tao bo sinh so ngau nhien
	food hq;
	Snake snake;
	KhoiTao(snake, hq);
	while (1)
	{
		//hien thi
		HienThi(snake, hq);
		gotoXY(51, 2);
		printf("Dieu khien bang a,s,d,w ");
		//dieu khien;
		DieuKhien(snake);
		//xu ly game
		ma = Xuly(snake, hq, thoigian);
		//thua ,thang
		if (ma == -1) //thua
		{
			gotoXY(51, 4);
			printf(" YOU LOSE\n");
			gotoXY(51, 6);
			printf("enter de thoat game");
			while (_getch() != 13);
			break;
		}
		//tocdo game
		Sleep(thoigian);
	}

	return 0;
}
