# 인벤토리 시스템 프로젝트

## 프로젝트 개요
템플릿 기반 인벤토리 시스템을 구현한 C++ 프로젝트입니다.  
- 아이템(Item) 클래스와 Inventory 템플릿 클래스를 사용하여 아이템 관리  
- 가격 기준 정렬 기능 제공  
- 인벤토리 자동 확장 기능 포함  

---

## 목차
- [main.cpp](#1-maincpp)  
- [Inventory.h](#2-inventoryh)  
- [Item.h](#3-itemh)  
- [compare.h](#4-compareh)  

---

### 1 main.cpp
- **역할**: 인벤토리 생성, 아이템 추가, 정렬 테스트
```cpp
#include <iostream>
#include "Inventory.h"
#include "Item.h"
#include "compare.h"

using namespace std;

int main() {
    Inventory<Item> inv;

    // 아이템 추가
    inv.AddItem(Item("검", 100));
    inv.AddItem(Item("포션", 50));
    inv.AddItem(Item("방패", 150));

    cout << "정렬 전:\n";
    inv.PrintAllItems();

    // 가격 기준 정렬
    inv.SortItems(compareItemsByPrice);

    cout << "\n정렬 후:\n";
    inv.PrintAllItems();

    return 0;
}
```

### 2 Inventory.h
역할: 템플릿 기반 인벤토리 관리
주요 기능:
아이템 추가/삭제
크기 자동 확장
복사/할당 지원
정렬 기능
```cpp
#pragma once
#include <iostream>
#include <algorithm>
using namespace std;

template <typename T>
class Inventory {
private:
    T* pItems_;
    int capacity_;
    int size_;

public:
    Inventory(int capacity = 10) {
        capacity_ = (capacity <= 0) ? 1 : capacity;
        size_ = 0;
        pItems_ = new T[capacity_];
    }

    Inventory(const Inventory<T>& other) {
        capacity_ = other.capacity_;
        size_ = other.size_;
        pItems_ = new T[capacity_];
        for (int i = 0; i < size_; i++) {
            pItems_[i] = other.pItems_[i];
        }
        cout << "인벤토리 복사 완료" << endl;
    }

    void Assign(const Inventory<T>& other) {
        if (this == &other) return;
        delete[] pItems_;
        capacity_ = other.capacity_;
        size_ = other.size_;
        pItems_ = new T[capacity_];
        for (int i = 0; i < size_; i++) {
            pItems_[i] = other.pItems_[i];
        }
    }

    ~Inventory() {
        delete[] pItems_;
        pItems_ = nullptr;
    }

    void Resize(int newCapacity) {
        if (newCapacity <= capacity_) return;
        T* newItems = new T[newCapacity];
        for (int i = 0; i < size_; i++) {
            newItems[i] = pItems_[i];
        }
        delete[] pItems_;
        pItems_ = newItems;
        capacity_ = newCapacity;
    }

    void AddItem(const T& item) {
        if (size_ >= capacity_) {
            cout << "자동 확장 실행 (x2)\n";
            Resize(capacity_ * 2);
        }
        pItems_[size_] = item;
        size_++;
    }

    void RemoveLastItem() {
        if (size_ > 0) size_--;
        else cout << "인벤토리가 비어있습니다." << endl;
    }

    int GetSize() const { return size_; }
    int GetCapacity() const { return capacity_; }

    void SortItems(bool (*comp)(const T&, const T&)) {
        sort(pItems_, pItems_ + size_, comp);
    }

    void PrintAllItems() const {
        if (size_ == 0) {
            cout << "비어있음" << endl;
            return;
        }
        for (int i = 0; i < size_; i++) {
            pItems_[i].PrintInfo();
        }
    }
};
```
### 3 Item.h

역할: 아이템 클래스 정의

속성: 이름, 가격

주요 기능: 정보 출력, 가격 반환
```cpp
#pragma once
#include <iostream>
#include <string>
using namespace std;

class Item {
private:
    string name_;
    int price_;

public:
    Item(string name = "", int price = 0) {
        name_ = name;
        price_ = price;
    }

    void PrintInfo() const {
        cout << "[이름: " << name_ << ", 가격: " << price_ << "G]" << endl;
    }

    int GetPrice() const { return price_; }
};
```
### 4 compare.h
역할: 아이템 정렬 비교 함수 정의 
```cpp
#pragma once
#include "Item.h"

bool compareItemsByPrice(const Item& a, const Item& b) {
    return a.GetPrice() < b.GetPrice();
}
