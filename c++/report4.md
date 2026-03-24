# 연금술 작업장 프로젝트

## 프로젝트 개요  
- 레시피 등록 및 조회 기능  
- 재료 검색 및 물약 지급 기능  
- 재고 관리 및 반환 기능  

---

## 목차
- [main.cpp](#1-maincpp)  
- [AlchemyWorkshop.h](#2-alchemyworkshoph)  
- [Recipe.h](#3-recipeh)  

---

### 1 main.cpp
- **역할**: 연금술 작업장 사용 예제 실행  
- **기능**: 레시피 추가, 이름/재료 검색, 물약 지급 및 반환
```cpp
#include <iostream>
#include "AlchemyWorkshop.h"

int main() {
    AlchemyWorkshop workshop;

    std::vector<Ingredient> ingredients1 = {
        {"간장", 1}, {"계란", 1}, {"참기름", 1}
    };
    workshop.AddRecipe({ "간장계란밥", ingredients1, 100 });

    std::vector<Ingredient> ingredients2 = {
        {"양파", 1}, {"당근", 1}, {"소고기", 1}
    };
    workshop.AddRecipe({ "소고기 야채볶음", ingredients2, 150 });

    std::cout << "=== 이름 검색 ===\n";
    const Recipe* recipe = workshop.FindRecipeByName("간장계란밥");
    if (recipe) {
        std::cout << recipe->GetName() << std::endl;
    }

    std::cout << "\n=== 재료 검색 ===\n";
    auto list = workshop.FindRecipesByIngredient("계란");
    for (const auto* r : list) {
        std::cout << r->GetName() << std::endl;
    }

    std::cout << "\n=== 재료로 지급 ===\n";
    if (workshop.ProvidePotionByKeyword("계란")) {
        std::cout << "계란으로 물약 지급 성공\n";
    }
    else {
        std::cout << "지급 실패\n";
    }

    std::cout << "\n=== 반환 ===\n";
    workshop.ReturnPotion("간장계란밥");

    return 0;
}
```
### 2 AlchemyWorkshop.h

역할: 연금술 작업장 클래스 정의

기능: 레시피 관리, 재료 검색, 물약 지급 및 반환, 재고 관리
```cpp
#pragma once
#include <map>
#include <vector>
#include "Recipe.h"

class AlchemyWorkshop {
private:
    std::map<std::string, Recipe> recipes_;
    std::map<std::string, int> inventory_;

public:
    void AddRecipe(const Recipe& recipe) {
        recipes_[recipe.GetName()] = recipe;
        inventory_[recipe.GetName()] = 3;
    }

    const Recipe* FindRecipeByName(const std::string& name) const {
        auto it = recipes_.find(name);
        if (it != recipes_.end()) {
            return &it->second;
        }
        return nullptr;
    }

    std::vector<const Recipe*> FindRecipesByIngredient(const std::string& ingredient) const {
        std::vector<const Recipe*> matchingRecipes;
        for (const auto& pair : recipes_) {
            const auto& recipe = pair.second;
            for (const auto& item : recipe.GetIngredients()) {
                if (item.name == ingredient) {
                    matchingRecipes.push_back(&recipe);
                    break;
                }
            }
        }
        return matchingRecipes;
    }

    const Recipe* FindByNameOrIngredient(const std::string& keyword) const {
        auto it = recipes_.find(keyword);
        if (it != recipes_.end()) return &it->second;

        for (const auto& pair : recipes_) {
            const auto& recipe = pair.second;
            for (const auto& ing : recipe.GetIngredients()) {
                if (ing.name == keyword) {
                    return &recipe;
                }
            }
        }
        return nullptr;
    }

    bool ProvidePotion(const std::string& name) {
        auto it = inventory_.find(name);
        if (it != inventory_.end() && it->second > 0) {
            it->second--;
            return true;
        }
        return false;
    }

    bool ProvidePotionByKeyword(const std::string& keyword) {
        const Recipe* r = FindByNameOrIngredient(keyword);
        if (!r) return false;

        auto it = inventory_.find(r->GetName());
        if (it != inventory_.end() && it->second > 0) {
            it->second--;
            return true;
        }
        return false;
    }

    void ReturnPotion(const std::string& name) {
        auto it = inventory_.find(name);
        if (it != inventory_.end() && it->second < 3) {
            it->second++;
        }
    }
};
```
### 3 Recipe.h

역할: 레시피 및 재료 클래스 정의

속성: 레시피 이름, 재료 리스트, 가격

기능: 정보 반환
```cpp
#pragma once
#include <string>
#include <vector>

class Ingredient {
public:
    std::string name;
    int amount;

    Ingredient(const std::string& name, int amount) : name(name), amount(amount) {}
};

class Recipe {
private:
    std::string name_;
    std::vector<Ingredient> ingredients_;
    int price_;

public:
    Recipe(const std::string& name, const std::vector<Ingredient>& ingredients, int price)
        : name_(name), ingredients_(ingredients), price_(price) {
    }

    const std::string& GetName() const { return name_; }
    const std::vector<Ingredient>& GetIngredients() const { return ingredients_; }
    int GetPrice() const { return price_; }
};
