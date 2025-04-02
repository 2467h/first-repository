#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 定义常量
#define MAX_FILES 100               // 最大文件数量
#define MAX_FILENAME_LENGTH 50      // 文件名最大长度
#define MAX_USERS 10                // 最大用户数量
#define MAX_USERNAME_LENGTH 20      // 用户名最大长度
#define MAX_PASSWORD_LENGTH 20      // 密码最大长度

// 定义文件条目结构体
typedef struct {
	char filename[MAX_FILENAME_LENGTH];  // 文件名
	int physical_address;                // 物理地址（文件编号）
	int protection_code;                 // 保护码（0: 只读, 1: 可读写）
	int length;                          // 文件长度
} FileEntry;

// 定义用户结构体
typedef struct {
	char username[MAX_USERNAME_LENGTH];  // 用户名
	char password[MAX_PASSWORD_LENGTH];  // 密码
} User;

// 全局变量
FileEntry file_table[MAX_FILES];  // 文件表，存储所有文件信息
int file_count = 0;               // 当前文件数量

User users[MAX_USERS];            // 用户表，存储所有用户信息
int user_count = 0;               // 当前用户数量

int current_user = -1;            // 当前登录用户索引，-1表示未登录

// 登录函数
void login() {
	char username[MAX_USERNAME_LENGTH];
	char password[MAX_PASSWORD_LENGTH];
	
	// 输入用户名和密码
	printf("Enter username: ");
	scanf("%s", username);
	printf("Enter password: ");
	scanf("%s", password);
	
	// 遍历用户表，验证用户名和密码
	for (int i = 0; i < user_count; i++) {
		if (strcmp(users[i].username, username) == 0 && strcmp(users[i].password, password) == 0) {
			current_user = i;  // 登录成功，设置当前用户
			printf("Login successful!\n");
			return;
		}
	}
	
	// 登录失败
	printf("Login failed. Invalid username or password.\n");
}

// 列文件目录函数
void dir() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	// 打印文件目录表头
	printf("Filename\tPhysical Address\tProtection Code\tLength\n");
	
	// 遍历文件表，打印每个文件的信息
	for (int i = 0; i < file_count; i++) {
		printf("%s\t\t%d\t\t\t%d\t\t%d\n", file_table[i].filename, file_table[i].physical_address, file_table[i].protection_code, file_table[i].length);
	}
}

// 创建文件函数
void create() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	char filename[MAX_FILENAME_LENGTH];
	int protection_code;
	
	// 输入文件名和保护码
	printf("Enter filename: ");
	scanf("%s", filename);
	printf("Enter protection code (0: read-only, 1: read-write): ");
	scanf("%d", &protection_code);
	
	// 检查文件表是否已满
	if (file_count >= MAX_FILES) {
		printf("Cannot create more files. File table is full.\n");
		return;
	}
	
	// 将文件信息添加到文件表中
	strcpy(file_table[file_count].filename, filename);
	file_table[file_count].physical_address = file_count;  // 物理地址为文件编号
	file_table[file_count].protection_code = protection_code;
	file_table[file_count].length = 0;  // 初始文件长度为0
	
	file_count++;  // 文件数量增加
	printf("File created successfully.\n");
}

// 删除文件函数
void delete() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	char filename[MAX_FILENAME_LENGTH];
	
	// 输入要删除的文件名
	printf("Enter filename to delete: ");
	scanf("%s", filename);
	
	// 遍历文件表，查找要删除的文件
	for (int i = 0; i < file_count; i++) {
		if (strcmp(file_table[i].filename, filename) == 0) {
			// 将后面的文件向前移动，覆盖要删除的文件
			for (int j = i; j < file_count - 1; j++) {
				file_table[j] = file_table[j + 1];
			}
			file_count--;  // 文件数量减少
			printf("File deleted successfully.\n");
			return;
		}
	}
	
	// 文件未找到
	printf("File not found.\n");
}

// 打开文件函数
void open() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	char filename[MAX_FILENAME_LENGTH];
	
	// 输入要打开的文件名
	printf("Enter filename to open: ");
	scanf("%s", filename);
	
	// 遍历文件表，查找要打开的文件
	for (int i = 0; i < file_count; i++) {
		if (strcmp(file_table[i].filename, filename) == 0) {
			// 检查文件保护码
			if (file_table[i].protection_code == 0) {
				printf("File is read-only.\n");
			} else {
				printf("File opened successfully.\n");
			}
			return;
		}
	}
	
	// 文件未找到
	printf("File not found.\n");
}

// 关闭文件函数
void close() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	// 关闭文件
	printf("File closed.\n");
}

// 读文件函数
void read() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	char filename[MAX_FILENAME_LENGTH];
	
	// 输入要读取的文件名
	printf("Enter filename to read: ");
	scanf("%s", filename);
	
	// 遍历文件表，查找要读取的文件
	for (int i = 0; i < file_count; i++) {
		if (strcmp(file_table[i].filename, filename) == 0) {
			printf("Reading file: %s\n", filename);
			return;
		}
	}
	
	// 文件未找到
	printf("File not found.\n");
}

// 写文件函数
void write() {
	// 检查是否已登录
	if (current_user == -1) {
		printf("Please login first.\n");
		return;
	}
	
	char filename[MAX_FILENAME_LENGTH];
	
	// 输入要写入的文件名
	printf("Enter filename to write: ");
	scanf("%s", filename);
	
	// 遍历文件表，查找要写入的文件
	for (int i = 0; i < file_count; i++) {
		if (strcmp(file_table[i].filename, filename) == 0) {
			// 检查文件保护码
			if (file_table[i].protection_code == 0) {
				printf("File is read-only. Cannot write.\n");
			} else {
				printf("Writing to file: %s\n", filename);
			}
			return;
		}
	}
	
	// 文件未找到
	printf("File not found.\n");
}

// 主函数
int main() {
	// 添加一些初始用户
	strcpy(users[0].username, "user1");
	strcpy(users[0].password, "pass1");
	user_count++;
	
	strcpy(users[1].username, "user2");
	strcpy(users[1].password, "pass2");
	user_count++;
	
	int choice;
	while (1) {
		// 打印主菜单
		printf("\n1. Login\n2. Dir\n3. Create\n4. Delete\n5. Open\n6. Close\n7. Read\n8. Write\n9. Exit\n");
		printf("Enter your choice: ");
		scanf("%d", &choice);
		
		// 根据用户选择执行相应操作
		switch (choice) {
		case 1:
			login();
			break;
		case 2:
			dir();
			break;
		case 3:
			create();
			break;
		case 4:
			delete();
			break;
		case 5:
			open();
			break;
		case 6:
			close();
			break;
		case 7:
			read();
			break;
		case 8:
			write();
			break;
		case 9:
			exit(0);  // 退出程序
			default:
				printf("Invalid choice. Please try again.\n");
		}
	}
	
	return 0;
}
