#include <windows.h>
#include <iostream>

using namespace std;

int* arr;          // Указатель на массив
int siz;           // Размер массива

// Функция вспомогательного потока (сортировка)
DWORD WINAPI Helper(LPVOID)
{
    cout << "Thread Helper started (sorting array)" << endl;

    // Сортировка пузырьком
    for (int i = 0; i < siz - 1; i++) {
        for (int j = 0; j < siz - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                // Меняем элементы местами
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }

    cout << "Thread Helper finished" << endl;
    return 0;
}

int main()
{
    cout << "Enter array size: ";
    cin >> siz;

    // Выделяем память для массива
    arr = new int[siz];

    // Заполняем массив
    cout << "Enter " << siz << " elements: ";
    for (int i = 0; i < siz; i++) {
        cin >> arr[i];
    }

    // Выводим исходный массив
    cout << "Original array: ";
    for (int i = 0; i < siz; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;

    // Создаем поток для сортировки
    HANDLE hThread = CreateThread(NULL, 0, Helper, NULL, 0, NULL);
    if (hThread == NULL) {
        cout << "Error creating thread!" << endl;
        delete[] arr;
        return 1;
    }

    // Ждем завершения потока
    WaitForSingleObject(hThread, INFINITE);

    // Закрываем дескриптор потока
    CloseHandle(hThread);

    // Выводим отсортированный массив
    cout << "Sorted array: ";
    for (int i = 0; i < siz; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;

    // Освобождаем память
    delete[] arr;

    return 0;
}
