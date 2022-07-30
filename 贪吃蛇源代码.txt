#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>
#include <conio.h>
#include <time.h>

#define SPACE 32 //暂停
#define ESC 27

struct Snake //蛇的结构
{
	int size;//蛇长
	int dir;//方向
	POINT coor[100];//蛇坐标
}snake;

struct Food//食物
{
	int x;
	int y;
	bool flag;//标记存在
	int grade;
	int max;
}food;

enum DIR//枚举，表示蛇的方向
{
	UP,
	DOWN,
	LEFT,
	RIGHT,
};

void Welcome();//欢迎
void CreatMap();//创建地图
void gotoxy(int x,int y);//光标跳转
void InitSnake();//初始化蛇
void Draw(int flag);//打印和覆盖蛇，打印食物
void keyControl();//键盘
void SnakeMove();//蛇移动
void eatFood();//吃食物
void HideCursor();//隐藏光标
int Judge();//判断死亡
void Over();//游戏结束页面
void ReadGrade();//从文件读取最高分
void WriteGrade();//更新最高分到文件



int main()
{
#pragma warning (disable:4996)
	int a = 1;
	system("mode con cols=100 lines=34");
	Welcome();
	InitSnake();
	HideCursor();
	ReadGrade();
	while (a)//游戏时循环
	{	
		CreatMap();
		Draw(1);
		keyControl();
		SnakeMove();
		eatFood();
		a = Judge();
	}
	system("cls");
	while (1)//游戏结束后
	{
		Over();
	}
	return 0;
}


void Welcome()
{
	printf("\n\n\n\n\n\n\n");
	printf("\t\t\t=================================================\n");
	printf("\t\t\t=\t\t\t\t\t\t=\n");
	printf("\t\t\t=\t\t欢迎来到贪吃蛇\t\t\t=\n");
	printf("\t\t\t=\t\t\t\t\t\t=\n");
	printf("\t\t\t=\t\t创作者：程君成\t\t\t=\n");
	printf("\t\t\t=\t\t\t\t\t\t=\n");
	printf("\t\t\t=================================================\n");
	system("pause");
	system("cls");
}

void CreatMap()
{
	
	for (int i = 0; i < 30; i++)
	{
		gotoxy(2 * i, 0);
		printf("■"); //占2个x，1个y
		gotoxy(2 * i, 29);
		printf("■");
	}
	for (int i =0; i < 30; i++)
	{
		gotoxy(0, i);
		printf("■");
		gotoxy(58, i);
		printf("■");
	}
	gotoxy(70, 10);
	printf("●您的最高分数：%d", food.max);
	gotoxy(70, 14);
	printf("●您的当前分数：%d", food.grade);
	gotoxy(70, 18);
	printf("●游戏提示：");
	gotoxy(70, 19);
	printf("  W-A-S-D：控制方向");
	gotoxy(70, 21);
	printf("  R键：重新开始游戏");
	gotoxy(70, 23);
	printf("  空格：暂停游戏");
	gotoxy(70, 25);
	printf("  Esc：退出游戏");
}

void gotoxy(int x, int y)
{
	COORD pos = { x,y };
	HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleCursorPosition(hOut, pos);
}

void InitSnake()
{
	snake.size = 6;
	snake.dir = RIGHT;
	food.max = 0;
	for (int i = 0; i < snake.size; i++)
	{
		snake.coor[i].x = 26 - 2 * i;
		snake.coor[i].y = 16;
	}
	srand((unsigned int)time(NULL));
	food.x = (rand() % 28 + 1)*2;
	food.y = rand() % 28 + 1;
	food.flag = true;
	food.grade = 0;
}

void SnakeMove()//蛇的移动
{
	Draw(0);
	for (int i = snake.size - 1; i > 0; i--)//让身体跟着头动
	{
		snake.coor[i] = snake.coor[i - 1];
	}
	switch (snake.dir)
	{
	case UP:
		snake.coor[0].y -= 1;
		
		break;
	case DOWN:
		snake.coor[0].y += 1;
		
		break;
	case LEFT:
		snake.coor[0].x -= 2;
		
		break;
	case RIGHT:
		snake.coor[0].x += 2;
		
		break;
	}
	Draw(1);
	Sleep(100);//改变蛇的速度
}


void keyControl()//通过按键改变蛇方向
{
	if (_kbhit())//检测有无按键输入,有按键改变方向，无则snakeMove
	{
		switch (_getch())//conio.h中的getch不需要回车
		{				//72 80 75 77 上下左右键值
		case 'w':
		case 'W':
		case 72:
			if (snake.dir != DOWN)
				snake.dir = UP;//防掉头
			break;
		case 's':
		case 'S':
		case 80:
			if (snake.dir != UP)
				snake.dir = DOWN;
			break;
		case 'a':
		case 'A':
		case 75:
			if (snake.dir != RIGHT)
				snake.dir = LEFT;
			break;
		case 'd':
		case 'D':
		case 77:
			if (snake.dir != LEFT)
				snake.dir = RIGHT;
			break;
		case SPACE: //暂停
			system("pause>nul"); //暂停后按任意键继续
			break;
		case ESC: //退出
			system("cls"); //清空屏幕
			gotoxy(40, 15);
			printf("  游戏结束  ");
			gotoxy(40, 15);
			exit(0);
		case 'r':
		case 'R': //重新开始
			system("cls"); //清空屏幕
			main(); //重新执行主函数
		}

	}
}

void Draw(int flag)
{
	if(flag==1)
	{
		for (int i = 0; i < snake.size; i++)
		{
			gotoxy(snake.coor[i].x, snake.coor[i].y);
			printf("■");
		}
	}
	else
	{
		for (int i = 0; i < snake.size; i++)
		{
			gotoxy(snake.coor[i].x, snake.coor[i].y);
			printf("  ");
		}
	}

	gotoxy(food.x, food.y);
	printf("●");
}

void eatFood()
{
	if (food.flag && snake.coor[0].x == food.x &&  snake.coor[0].y == food.y )
		//食物存在
	{
		food.flag = false;
		snake.size++;
		food.grade += 10;
	}
	//食物消失
	if (!food.flag)
	{
		food.x = (rand() % 28 + 1) * 2;
		food.y = rand() % 28 + 1;
		food.flag = true;
	}
}

void HideCursor()
{
	CONSOLE_CURSOR_INFO curInfo; //定义光标信息的结构体变量
	curInfo.dwSize = 1; //如果没赋值的话，光标隐藏无效
	curInfo.bVisible = FALSE; //将光标设置为不可见
	HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE); //获取控制台句柄
	SetConsoleCursorInfo(handle, &curInfo); //设置光标信息
}

int Judge()
{
	for(int i=1;i<snake.size;i++)
	{
		if (snake.coor[0].x == snake.coor[i].x &&
			snake.coor[0].y == snake.coor[i].y)
		{
			Sleep(700);
			return 0;
		}
	}
	if (snake.coor[0].x == 0 || snake.coor[0].x == 58 ||
		snake.coor[0].y == 0 || snake.coor[0].y == 29)
	{
		Sleep(700);
		return 0;
	}
}

void Over()
{
	gotoxy(32, 8);
	if (food.grade > food.max)
	{
		printf("恭喜你打破最高记录，最高记录更新为%d", food.grade);
		WriteGrade();//写max进文件
	}
	else if (food.grade == food.max)
	{
		printf("与最高记录持平，加油再创佳绩");
	}
	else
	{
		printf("请继续加油，当前与最高记录相差%d", food.max - food.grade);
	}
	gotoxy(40, 10);
	printf("GAME OVER");
	
	while (1) //询问玩家是否再来一局
	{
		char ch;
		gotoxy(38, 14);
		printf("再来一局?(y/n):");
		scanf("%c", &ch);
		if (ch == 'y' || ch == 'Y')
		{
			system("cls");
			main();
		}
		else if (ch == 'n' || ch == 'N')
				{
					exit(0);
				}
		else
		{
			gotoxy(36, 16);
			printf("选择错误，请再次选择");
		}
	}
}


void ReadGrade()
{
	FILE* pf = fopen("贪吃蛇最高得分记录.txt", "r"); 
	if (pf == NULL) 
	{
		pf = fopen("贪吃蛇最高得分记录.txt", "w"); 
		fwrite(&food.max, sizeof(int), 1, pf);
	}
	fseek(pf, 0, SEEK_SET);
	fread(&food.max, sizeof(int), 1, pf);
	fclose(pf); 
	pf = NULL; 
}


void WriteGrade()
{
	FILE* pf = fopen("贪吃蛇最高得分记录.txt", "w"); 
	if (pf == NULL) 
	{
		printf("保存最高得分记录失败\n");
		exit(0);
	}
	fwrite(&food.grade, sizeof(int), 1, pf); //将本局游戏得分写入
	fclose(pf); 
	pf = NULL; 
}

