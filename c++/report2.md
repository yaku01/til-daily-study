1. 프로젝트 개요

설명: 플레이어의 HP, MP, 공격력, 방어력, 레벨, 운(luck)을 관리하며, 포션 사용과 레벨업 기능이 포함되어 있습니다.

사용 언어/환경: C++

2. 클래스별 설명

Player 클래스

역할: 모든 플레이어의 공통 속성을 관리

주요 멤버: name, job, level, hp, mp, power, def, accuracy, speed

주요 함수:

attack() : 순수 가상 함수

attack(Monster* monster) : 몬스터 공격

printPlayerStatus() : 상태 출력

Getter/Setter 제공

Warrior 클래스

Player 상속
공격력과 방어력, HP/MP 초기화
attack()와 attack(Monster* monster) 오버라이드
Monster 클래스
역할: 플레이어와 전투를 하는 몬스터 관리
주요 멤버: name, HP, power, defence, speed
주요 함수:
attack(Player* player) : 플레이어 공격
Getter/Setter 제공

3. 코드

// main.cpp

#include <iostream>
#include "Player.h"
#include "Warrior.h"
#include "Magician.h"
#include "Archer.h"
#include "Thief.h"
#include "Monster.h"

using namespace std;

int main()
{
    Player* player = nullptr;
    int job;
    string name;

    cout << "당신의 닉네임을 입력하세요: ";
    cin >> name;

    cout << "\n===== 직업 선택 =====\n";
    cout << "1. 전사\n";
    cout << "2. 마법사\n";
    cout << "3. 도적\n";
    cout << "4. 궁수\n";
    cout << "번호 선택: ";
    cin >> job;

    switch (job)
    {
        case 1: player = new Warrior(name); break;
        case 2: player = new Magician(name); break;
        case 3: player = new Thief(name); break;
        case 4: player = new Archer(name); break;
        default:
            cout << "잘못된 입력입니다.\n";
            return 0;
    }

    cout << "\n===== 캐릭터 정보 =====\n";
    player->printPlayerStatus();

    Monster m("슬라임");

    cout << "\n===== 전투 시작! =====\n";

    while (player->getHP() > 0 && m.getHP() > 0)
    {
        player->attack(&m);

        if (m.getHP() <= 0) break;

        m.attack(player);

        if (player->getHP() <= 0) break;
    }

    if (player->getHP() <= 0)
        cout << player->getName() << " 패배!\n";
    else
        cout << player->getName() << " 승리!\n";

    delete player;

    return 0;
}


// Player.h

#pragma once
#include <string>
#include <iostream>
using namespace std;

class Monster;

class Player
{
public:
    Player(string name);
    virtual ~Player();

    virtual void attack() = 0;
    virtual void attack(Monster* monster);

    void printPlayerStatus();

    string getName();
    string getJob();
    int getLevel();
    int getHP();
    int getDef();

    void setHP(int hp);
    void setMP(int mp);
    void setPower(int power);
    void setDef(int def);
    void setAccuracy(int accuracy);
    void setSpeed(int speed);

protected:
    string name;
    string job;
    int level;
    int hp;
    int mp;
    int power;
    int def;
    int accuracy;
    int speed;
};


// Player.cpp
#include "Player.h"
#include "Monster.h"

Player::Player(string name) {
    this->name = name;
    this->job = "";
    this->level = 1;
    this->hp = 100;
    this->mp = 50;
    this->power = 10;
    this->def = 5;
    this->accuracy = 80;
    this->speed = 10;
}

Player::~Player() {}

void Player::attack(Monster* monster) {
    int damage = power - monster->getDef();
    if (damage <= 0) damage = 1;

    int newHP = monster->getHP() - damage;
    if (newHP < 0) newHP = 0;

    monster->setHP(newHP);

    cout << name << "이(가) " << monster->getName() << "을(를) 공격했다! 데미지: " << damage << "\n";

    if (monster->getHP() > 0)
        cout << monster->getName() << "의 남은 HP: " << monster->getHP() << "\n";
    else
        cout << monster->getName() << "을(를) 처치했다! 축하!\n";
}

void Player::printPlayerStatus() {
    cout << "닉네임: " << name << "\n";
    cout << "직업: " << job << "\n";
    cout << "레벨: " << level << "\n";
    cout << "HP: " << hp << "\n";
    cout << "MP: " << mp << "\n";
    cout << "힘: " << power << "\n";
    cout << "방어력: " << def << "\n";
    cout << "정확도: " << accuracy << "\n";
    cout << "속도: " << speed << "\n";
}

string Player::getName() { return name; }
string Player::getJob() { return job; }
int Player::getLevel() { return level; }
int Player::getHP() { return hp; }
int Player::getDef() { return def; }

void Player::setHP(int hp) { this->hp = hp; }
void Player::setMP(int mp) { this->mp = mp; }
void Player::setPower(int power) { this->power = power; }
void Player::setDef(int def) { this->def = def; }
void Player::setAccuracy(int accuracy) { this->accuracy = accuracy; }
void Player::setSpeed(int speed) { this->speed = speed; }
// Warrior.h
#pragma once
#include "Player.h"
#include "Monster.h"

class Warrior : public Player
{
public:
    Warrior(std::string name);
    virtual ~Warrior();

    void attack() override;
    void attack(Monster* monster) override;
};


// Warrior.cpp


#include "Warrior.h"
#include <iostream>
using namespace std;

Warrior::Warrior(string name) : Player(name)
{
    job = "Warrior";
    hp = 100;
    mp = 50;
    power = 5;
    def = 30;
    accuracy = 85;
    speed = 5;
}

Warrior::~Warrior() {}

void Warrior::attack()
{
    cout << name << "이(가) 검으로 공격한다! \n";
}

void Warrior::attack(Monster* monster)
{
    int damage = power - monster->getDef();
    if (damage <= 0) damage = 1;

    int newHP = monster->getHP() - damage;
    if (newHP < 0) newHP = 0;

    monster->setHP(newHP);

    cout << name << "이(가) " << monster->getName() << "에게 검으로 " << damage << "의 피해를 입혔다!\n";

    if (monster->getHP() > 0)
        cout << monster->getName() << "의 남은 HP: " << monster->getHP() << "\n";
    else
        cout << monster->getName() << "을 처치했다!\n";
}
// Monster.h
#pragma once
#include <string>
#include <iostream>
using namespace std;

class Player;

class Monster {
public:
    Monster(string name);

    void attack(Player* player);

    string getName();
    int getHP();
    int getPower();
    int getDef();
    int getSpeed();

    void setName(string name);
    void setHP(int HP);
    void setPower(int power);
    void setDef(int def);
    void setSpeed(int speed);

protected:
    string name;
    int HP;
    int power;
    int defence;
    int speed;
};


// Monster.cpp


#include "Monster.h"
#include "Player.h"

Monster::Monster(string name) {
    this->name = name;
    this->HP = 10;
    this->power = 30;
    this->defence = 10;
    this->speed = 10;
}

void Monster::attack(Player* player) {
    int damage = power - player->getDef();
    if (damage <= 0) damage = 1;

    int newHP = player->getHP() - damage;
    if (newHP < 0) newHP = 0;

    player->setHP(newHP);

    cout << name << "이(가) " << player->getName() << "을(를) 공격했다! 데미지: " << damage << "\n";

    if (player->getHP() > 0)
        cout << player->getName() << "의 남은 HP: " << player->getHP() << "\n";
    else
        cout << player->getName() << "이(가) 쓰러졌다!\n";
}

string Monster::getName() { return name; }
int Monster::getHP() { return HP; }
int Monster::getPower() { return power; }
int Monster::getDef() { return defence; }
int Monster::getSpeed() { return speed; }

void Monster::setName(string name) { this->name = name; }
void Monster::setHP(int HP) { this->HP = HP; }
void Monster::setPower(int power) { this->power = power; }
void Monster::setDef(int def) { this->defence = def; }
void Monster::setSpeed(int speed) { this->speed = speed; }
