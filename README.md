# define _CRT_SECURE_NO_WARNINGS 1

# define MAX_NAME 30
# define MAX_SEX 5
# define MAX_TELE 20
# define MAX_ADDR 20
# define MAX_RELE 20

# define DEFAULT_SIZE 3

# include <stdio.h>
# include <string.h>
# include <stdlib.h> 

struct PeoInfo//通讯录中人员信息 
{
	char name[MAX_NAME];
	char sex[MAX_SEX];
	char tele[MAX_TELE];
	char addr[MAX_ADDR];
	char rele[MAX_RELE];
	int age;
};

//定义通讯录类型 
struct Contact
{
	struct PeoInfo* data;//定义通讯录数据库 
	int capacity;
	int size;//记录下一次存放位置
};

enum Option//枚举提高代码可读性 
{
	EXIT,//系统默认从零开始 
	SHOW,
	SEARCH,
	SORT,
	ADD,
	DELETE,
	MODIFY
};

void IntiContact(struct Contact* con);
void AddContact(struct Contact* con);
void ShowContact(const struct Contact* con);
void DelContact(struct Contact* con);
void SearchContact(const struct Contact* con);
void ModifyContact(struct Contact* con);
void SortContact(struct Contact* con);

//选择列表 
void menu()
{
	printf("\n");
	printf("\t1.显示\t\t2.查找\n");
	printf("\t3.排序\t\t4.添加\n");
	printf("\t5.删除\t\t6.修改\n");
	printf("\t0.退出\t\t      \n");
}

int main()
{
	struct Contact con;//创建一个通讯录 
	IntiContact(&con);//初始化通讯录 
	int input = 0;
	printf("通讯录具有以下功能： ");
	do//控制执行操作的退出与否 
	{
		menu();
		printf("请选择： ");
		scanf("%d", &input);
		switch (input)//以下break是跳出switch 
		{
		case SHOW:
			ShowContact(&con);
			break;
		case SEARCH:
			SearchContact(&con);
			break;
		case SORT:
			SortContact(&con);
			break;
		case ADD:
			AddContact(&con);
			break;
		case DELETE:
			DelContact(&con);
			break;
		case MODIFY:
			ModifyContact(&con);
			break;
		case EXIT:
			printf("EXIT");
			break;
		default:
			printf("选择错误!");
			break;
		}

	} while (input);

	return 0;
}

void IntiContact(struct Contact* con)
{
	//最开始默认三个人员数据 
	con->data = (struct PeoInfo*)malloc(DEFAULT_SIZE * sizeof(struct PeoInfo));
	memset(con->data, 0, sizeof(con->data));
	con->size = 0;
	con->capacity = DEFAULT_SIZE;
}

void CheckContact(struct Contact* con)
{
	//满员 
	if (con->size == con->capacity)
	{
		//增容 
		struct PeoInfo* redata;
		redata = (struct PeoInfo*)realloc(con->data, (con->capacity + 2) * sizeof(struct PeoInfo));
		if (redata != NULL)
		{
			con->data = redata;
			con->capacity += 2;
			printf("增容成功！\n");
		}
		if (redata == NULL)
		{
			printf("增容失败！\n");
		}
	}
}

void Mid_Add(struct Contact* con)
{
	printf("选择插入第几个位置：\n");
	int point;
	scanf("%d", &point);

	if (point > con->size || point < 1)
	{
	  printf("不存在该位置\n");
		printf("请重新选择\n");
		Mid_Add(con);
	}
	else
	{
		CheckContact(con);
		for (int i = con->size - 1; i >= point - 1; --i)
		{
			con->data[i + 1] = con->data[i];
		}
		printf("请输入姓名： ");
		scanf("%s", con->data[point - 1].name);
		printf("请输入年龄： ");
		scanf("%d", &con->data[point - 1].age);
		printf("请输入性别： ");
		scanf("%s", con->data[point - 1].sex);
		printf("请输入电话： ");
		scanf("%s", con->data[point - 1].tele);
		printf("请输入关系： ");
		scanf("%s", con->data[point - 1].rele);
		printf("请输入地址： ");
		scanf("%s", con->data[point - 1].addr);
	}
}

//实现添加功能 
void AddContact(struct Contact* con)
{
	CheckContact(con);

	printf("请选择需要的添加模式：\n");
	printf("                      1.默认添加\n");
	printf("                      2.中间插入\n");
	int t;
	scanf("%d", &t);

	if (t == 2)
	{
		Mid_Add(con);
	}
	if (t == 1)
	{
		printf("请输入姓名： ");
		scanf("%s", con->data[con->size].name);//name属于data变量下的成员，字符串数组可以直接%s赋值 
		printf("请输入年龄： ");
		scanf("%d", &con->data[con->size].age);
		printf("请输入性别： ");
		scanf("%s", con->data[con->size].sex);
		printf("请输入电话： ");
		scanf("%s", con->data[con->size].tele);
		printf("请输入关系： ");
		scanf("%s", con->data[con->size].rele);
		printf("请输入地址： ");
		scanf("%s", con->data[con->size].addr);
	}

	con->size++;
	printf("添加成功！");

	printf("是否继续添加用户？“是”填1  “不是”填0\n");
	int j;
	scanf("%d", &j);
	if (j == 1)
	{
		AddContact(con);
	}
	else
	{
		printf("已退出添加程序");
	}
}

//实现显示功能 
void ShowContact(const struct Contact* con)
{
	if (con->size == 0)
	{
		printf("无用户信息");
	}
	else
	{
		printf("%-20s\t%-5s\t%-5s\t%-15s\t%-12s\t%-15s\n", "姓名", "年龄", "性别", "电话", "关系", "地址");
		for (int i = 0; i < con->size; ++i)
		{
			printf("%-20s\t%-5d\t%-5s\t%-15s\t%-12s\t%-15s\n",
				con->data[i].name, con->data[i].age,
				con->data[i].sex, con->data[i].tele,
				con->data[i].rele, con->data[i].addr);
		}
	}
}

//用于调用的查找函数 
static int FindContact(const struct Contact* con, char name[])
{
	int j = 0;
	for (j; j < con->size; ++j)
	{
		if (0 == strcmp(con->data[j].name, name))//strcmp函数返回值为0表示相等 
		{
			return j;
			break;
		}
	}
	if (j == con->size)
	{
		return -1;
	}
}

//实现删除功能 
void DelContact(struct Contact* con)
{
	int result;
	char name[MAX_NAME];
	printf("请输入要删除的对象： ");
	scanf("%s", name);
	result = FindContact(con, name);//此处con相当于主函数的&con,接收返回的j值 

	if (result == -1)
	{
		printf("抱歉，查无此人\n");
	}
	else
	{
		int j;
		for (j = result; j < con->size - 1; ++j)
		{
			con->data[j] = con->data[j + 1];
		}
		con->size -= 1;//减去删除的角标个数 
		printf("删除成功！");
	}
	printf("是否继续删除用户？“是”填1  “不是”填0\n");
	int j;
	scanf("%d", &j);

	if (j == 1)
	{
		DelContact(con);
	}
	else
	{
		printf("已退出删除程序");
	}
}

//实现搜查功能 
void SearchContact(const struct Contact* con)
{
	int result;
	char name[MAX_NAME];
	printf("请输入要查找的对象： ");
	scanf("%s", name);
	result = FindContact(con, name);//记录该人下标 

	if (result == -1)
	{
		printf("抱歉，查无此人\n");
	}
	else
	{
		printf("%-20s\t%-5s\t%-5s\t%-15s\t%-12s\t%-15s\n", "姓名", "年龄", "性别", "电话", "关系", "地址");
		printf("%-20s\t%-5d\t%-5s\t%-15s\t%-12s\t%-15s\n",
			con->data[result].name, con->data[result].age,
			con->data[result].sex, con->data[result].tele,
			con->data[result].rele, con->data[result].addr);
	}
	printf("是否继续查找用户？“是”填1  “不是”填0\n");
	int j;
	scanf("%d", &j);

	if (j == 1)
	{
		SearchContact(con);
	}
	else
	{
		printf("已退出查找程序");
	}
}

void ModifyContact(struct Contact* con)
{
	int result;
	char name[MAX_NAME];
	printf("请输入要修改的对象： ");
	scanf("%s", name);
	result = FindContact(con, name);

	if (result == -1)
	{
		printf("抱歉，查无此人\n");
	}
	else
	{
		printf("请输入新名字： ");
		scanf("%s", con->data[result].name);
		printf("请输入新性别： ");
		scanf("%s", con->data[result].sex);
		printf("请输入新年龄： ");
		scanf("%d", &con->data[result].age);
		printf("请输入新关系： ");
		scanf("%s", con->data[result].rele);
		printf("请输入新电话： ");
		scanf("%s", con->data[result].tele);
		printf("请输入新地址： ");
		scanf("%s", con->data[result].addr);

		printf("修改成功！");
	}
	printf("是否继续修改用户？“是”填1  “不是”填0\n");
	int j;
	scanf("%d", &j);

	if (j == 1)
	{
		ModifyContact(con);
	}
	else
	{
		printf("已退出修改程序");
	}
}

void AgeSort(struct Contact* con)
{
	for (int i = 0; i < con->size; ++i)
	{
		for (int j = i + 1; j < con->size; ++j)
		{
			struct PeoInfo t;//因为con->data是data类型，也就是PeoInfo类型 
			if (con->data[j].age < con->data[i].age)
			{
				t = con->data[j];
				con->data[j] = con->data[i];
				con->data[i] = t;//是这个整体换，而非单个成员换 
			}
		}
	}
	printf("分类成功！");
}

void CaptSort(struct Contact* con)
{
	for (int i = 0; i < con->size; ++i)
	{
		for (int j = i + 1; j < con->size; ++j)
		{
			struct PeoInfo t;
			if (con->data[j].name[0] < con->data[i].name[0])
			{
				t = con->data[j];
				con->data[j] = con->data[i];
				con->data[i] = t;
			}
		}
	}
	printf("分类成功！");
}

void OrderSort(const struct Contact* con)
{
	printf("分类成功！");
}

void SortContact(struct Contact* con)
{
	printf("请选择需要分类的类型：\n");
	printf("                      1.年龄大小分类\n");
	printf("                      2.姓首字母分类\n");
	printf("                      3.默认顺序分类\n");
	char a;
	scanf("%d", &a);

	switch (a)
	{
	case 1: AgeSort(con);
		break;
	case 2: CaptSort(con);
		break;
	case 3: OrderSort(con);
		break;
	default: printf("选择错误！");
		break;
	}
}
