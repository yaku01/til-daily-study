1. 프로젝트 개요


설명:  


플레이어의 HP, MP, 공격력, 방어력, 레벨, 운(luck)을 관리하며, 포션 사용과 레벨업 기능이 포함되어 있습니다.


사용 언어/환경: C++


3. 클래스별 설명

Status 열거형:
캐릭터 스탯을 배열 인덱스로 관리
HP, MP, ATK, DEF, level, luck
setPotion 함수:
HP와 MP 포션을 증가시키는 함수
입력: 포션 개수, HP포션 포인터, MP포션 포인터
동작: 포인터로 전달된 포션 개수 증가
메인 루프 기능:
초기 HP, MP 입력 (50 이하 입력 시 재입력)
공격력, 방어력 입력 (0 이하 입력 시 재입력)
포션 지급 및 메뉴 선택 반복
HP/MP UP, 공격력/방어력 UP, 현재 능력치 출력, Level Up, luck UP, 종료
3.코드 
```cpp
#include <iostream>

// 캐릭터 스탯 인덱스 정의
enum Status {
	HP,     // 체력
	MP,     // 마나
	ATK,    // 공격력
	DEF,    // 방어력
	level,  // 레벨
	luck    // 운
};

// HP, MP 포션 개수를 증가시키는 함수
void setPotion(int count, int* p_HPPotion, int* p_MPPotion) {
	*p_HPPotion += count;
	*p_MPPotion += count;
}

using namespace std;

int main() {
	int status[6];        // 캐릭터 스탯 배열
	int menu;             // 메뉴 선택 변수
	int i = 0;            // 입력 단계 확인 변수
	int hpPotion = 0;     // HP 포션 보유량
	int mpPotion = 0;     // MP 포션 보유량

	// 초기 레벨과 luck 값 설정
  
	status[level] = 1;
	status[luck] = 0;

	// 초반 포션 지급
	setPotion(5, &hpPotion, &mpPotion);

	// 캐릭터 초기 스탯 입력 단계
	while (i < 2) {
		if (i == 0) {
			// HP, MP 입력
			cout << "HP MP를 입력하시오(하나라도50이하로적으면 재입력입니다)\n";
			cin >> status[HP] >> status[MP];

			if (status[HP] <= 50 || status[MP] <= 50) {
				cout << "다시적어주시기바랍니다.\n";
			} else {
				i++; // 다음 입력 단계로 이동
			}
		} else {
			// 공격력, 방어력 입력
			cout << "공격력과방어력을 입력하시오(하나라도0이하로적으면 재입력입니다)\n";
			cin >> status[ATK] >> status[DEF];

			if (status[ATK] <= 0 || status[DEF] <= 0) {
				cout << "다시적어주시기바랍니다.\n";
			} else {
				cout << "감사합니다.\n";
				cout << "첫포션이 지급되었습니다.\nHP포션:" << hpPotion << "\nMP포션:" << mpPotion;
				i++;
			}

			// 메뉴 선택 루프
			while (true) {
				cout << "=================================================================================\n";
				cout << "<선택지>\n";
				cout << "1.HP UP\n2.MP UP\n3.공격력 UP\n4.방어력 UP\n5.현재 능력치\n6.Level Up\n7.luck up\n0.나가기\n";
				cout << "입력:";
				cin >> menu;

				switch (menu) {
					case 0:
						// 프로그램 종료
						return 0;

					case 1:
						// HP 포션 사용
						if (hpPotion > 0) {
							cout << "HP포션을사용하였습니다.\n";
							hpPotion--;
							status[HP] += 20;
							cout << "현재HP:" << status[HP] << "\n현재포션보유량:" << hpPotion;
						} else {
							cout << "HP포션이 없습니다.";
						}
						break;

					case 2:
						// MP 포션 사용
						if (mpPotion > 0) {
							cout << "MP포션을사용하였습니다.\n";
							mpPotion--;
							status[MP] += 20;
							cout << "현재MP:" << status[MP] << "\n현재포션보유량:" << mpPotion;
						} else {
							cout << "MP포션이 없습니다.";
						}
						break;

					case 3:
						// 공격력 두 배
						status[ATK] *= 2;
						break;

					case 4:
						// 방어력 두 배
						status[DEF] *= 2;
						break;

					case 5:
						// 현재 스탯 출력
						cout << "<현재 스탯 상황>\n";
						cout << "Level:" << status[level];
						cout << "\nHP:" << status[HP];
						cout << "\nMP:" << status[MP];
						cout << "\n공격력:" << status[ATK];
						cout << "\n방어력:" << status[DEF];
						cout << "\nHP포션현황:" << hpPotion;
						cout << "\nMP포션현황:" << mpPotion;
						break;

					case 6:
						// 레벨 업 + 포션 1개 추가
						cout << "Level Up\n";
						status[level] += 1;
						cout << "HP,MP포션이 한개 추가됩니다.";
						setPotion(1, &hpPotion, &mpPotion);
						cout << "\nHP포션현황:" << hpPotion;
						cout << "\nMP포션현황:" << mpPotion;
						break;

					case 7:
						// luck 증가
						cout << "luck UP\n";
						status[luck] += 1;
						break;
				}
			}
		}
	}
}
