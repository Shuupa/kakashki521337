# kakashki521337
```c
#define _CRT_SECURE_NO_WARNINGS
#define TXTLEN_MAX 100 // максимальная длинна строки
#define WRDSDIF_MAX 50 // максимальное кол-во разных слов (костыль)
#define WRDLEN_MAX 30 // максимальная длинна слова

#include <stdio.h>
#include <ctype.h>

char tolower_1(char c) { // самописный туловер
	if (c > 64 && c < 91) { // по utf-8 берём большие буквы
		c = c + 32; // меняем на маленькие
	}
	return c;
}

int strlen_1(const char* str) // самописный стрлен
{
	int len = 0;
	while (str[len] != '\0') {
		len++;
	}
	return len;
}

void strcpy_1(char* destination, const char* source) // самописный стрцпу
{
	int i = 0;
	while (source[i] != '\0'){ // прогон до конца строки
		destination[i] = source[i]; // копируем по итому
		i++;
	}
	destination[i] = '\0'; // завершающий символ в конце
} // влияет только на destination, сурс текст не меняет по этому без ретёрнов

int strcasecmp_1(const char* str1, const char* str2) // самописный стркейсцмп
{
	int i = 0;
	while (str1[i] != '\0' && str2[i] != '\0') // до конца любой из строк
	{
		char c1 = tolower_1(str1[i]); // оба символа в нижний рег как в стандартном кейсцмп
		char c2 = tolower_1(str2[i]);

		if (c1 != c2) // если не равны возврат разницы
		{
			return c1 - c2;
		}
		i++;
	}
	return tolower_1(str1[i]) - tolower_1(str2[i]); // возврат для последнего символа если строки разной длины
}

int delimiter_1(char c) // проверка на разделитель
{
	return c == ' ' || c == ',' || c == '.' || c == '!' || c == '?' || c == ';' || c == ':' || c == '(' || c == ')' || c == '\t'; // вернёт 1 или 0 (тру фолс)
}


int find_isk(const char* wrd, char isk) // функа нахожджения заданного символа
{
	int i = 0;
	char isktl = tolower_1(isk);
	while (wrd[i] != '\0') // до конца слова
	{
		if (tolower_1(wrd[i]) == isktl) // проверяем есть ли буква
		{
			return 1; // если есть, возврат тру
		}
		i++;
	}
	return 0; // если нет, фолс
}

int main()
{
	printf("Прога написана без стандарнтых функций");
	int repeat = 1;

	do
	{
		char txt[TXTLEN_MAX + 1]; // для текста
		char isk; // символ который ищем
		char words[WRDSDIF_MAX][WRDLEN_MAX + 1]; // массив для хранения разных слов
		int word_count = 0; // количество уникальных слов
		int word_freq[WRDSDIF_MAX] = { 0 }; // частоты слов

		printf("\nВведите текст (макс %d символов): ", TXTLEN_MAX);
		int i = 0;
		char ch;

		ch = getchar();

		while (ch == '\n' || ch == ' ') {
			ch = getchar();
		}

		while (i < TXTLEN_MAX && ch != '\n' && ch != EOF) {
			txt[i++] = ch;
			ch = getchar();
		}
		txt[i] = '\0'; // завершающий нуль-символ

		if (strlen_1(txt) == 0)
		{
			printf("\nВведена пустая строка.");
		}
		else
		{
			printf("Введите символ для поиска: ");
			scanf(" %c", &isk);
			printf("Символ для поиска - %c\n", isk);
			while (getchar() != '\n');

			i = 0;
			while (txt[i] != '\0') {
				while (txt[i] != '\0' && delimiter_1(txt[i])) {
					i++;
				}

				if (txt[i] != '\0') {
					char current_word[WRDLEN_MAX + 1];
					int j = 0;

					while (txt[i] != '\0' && !delimiter_1(txt[i]) && j < WRDLEN_MAX) {
						current_word[j++] = txt[i++];
					}
					current_word[j] = '\0';

					if (strlen_1(current_word) > 0) {
						char word_lower[WRDLEN_MAX + 1];
						strcpy_1(word_lower, current_word);

						int k = 0;
						while (word_lower[k] != '\0') {
							word_lower[k] = tolower_1(word_lower[k]);
							k++;
						}

						int found_index = -1;
						for (int w = 0; w < word_count; w++) {
							char saved_lower[WRDLEN_MAX + 1];
							strcpy_1(saved_lower, words[w]);

							k = 0;
							while (saved_lower[k] != '\0') {
								saved_lower[k] = tolower_1(saved_lower[k]);
								k++;
							}

							if (strcasecmp_1(word_lower, saved_lower) == 0) {
								found_index = w;
								break;
							}
						}

						if (found_index != -1) {
							word_freq[found_index]++;
						}
						else if (word_count < WRDSDIF_MAX) {
							strcpy_1(words[word_count], current_word);
							word_freq[word_count] = 1;
							word_count++;
						}
					}
				}
			}
			int found_any = 0;
			for (int w = 0; w < word_count; w++) {
				if (word_freq[w] >= 2 && find_isk(words[w], isk)) {
					if (!found_any) {
						printf("Результат: ");
						found_any = 1;
					}
					printf("%s - встречается %d раз(a)\n", words[w], word_freq[w]);
				}
			}

			if (!found_any) {
				printf("Слов не найдено");
			}
			printf("Текст в котором искали - \"%s\"\n",txt);
			printf("\n");
		}

		printf("Повторить вычисления?");
		do
		{
			printf("\n1 - да, 0 - нет: ");
			scanf("%d", &repeat);
			while (getchar() != '\n');
		} while (repeat != 1 && repeat != 0);
	} while (repeat == 1);
}
```
