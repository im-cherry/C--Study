# 01 TextRPG

```c#
using System;
using System.Text;

namespace CSharp
{
    class Program
    {
        enum PlayerType  // 직업 종류
        {
            None,
            Knight,  // 기사
            Archer,  // 궁수
            Mage,  // 마법사
        }

        enum MonsterType
        {
            None,
            Slime,  // 슬라임
            Skeleton,  // 해골
            Orc  // 오크
        }

        struct Player  // 캐릭터 능력치
        {
            public PlayerType type;  // 직업
            public int hp;  // 체력
            public int attack;  // 공격력
        }

        struct Monster  // 몬스터 능력치
        {
            public MonsterType type;  // 몬스터 종류
            public int hp;  // 체력
            public int attack;  // 공격력
        }

        static void Main(string[] args)
        {
            Console.WriteLine("게임 시작\n");

            while(true)
            {
                PlayerType type = ChoosePlayerType();  // 직업 선택
                if(type == PlayerType.None)
                {
                    Console.WriteLine("게임 종료\n");
                    return;
                }

                Player player = CreatePlayer(type);  // 캐랙터 생성

                if(EnterTown(ref player))
                {
                    Console.WriteLine("로비로 돌아갑니다.");
                    continue;
                }
                else
                {
                    Console.WriteLine("\n게임 종료");
                    return;
                }
            }
        }

        // 직업을 선택하는 함수
        static PlayerType ChoosePlayerType()
        {
            while(true)
            {
                Console.WriteLine("직업을 선택하세요.");
                Console.WriteLine("[1] 기사");
                Console.WriteLine("[2] 궁수");
                Console.WriteLine("[3] 마법사");
                Console.WriteLine("[4] 게임 종료");

                string input = Console.ReadLine();
                switch(input)
                {
                    case "1":
                        return PlayerType.Knight;
                    case "2":
                        return PlayerType.Archer;
                    case "3":
                        return PlayerType.Mage;
                    case "4":
                        return PlayerType.None;
                }
            }
        }

        // 직업에 따라 플레이어(캐릭터)를 생성하는 함수
        static Player CreatePlayer(PlayerType type)
        {
            Player player;
            player.type = PlayerType.None;
            player.hp = 0;
            player.attack = 0;

            switch(type)
            {
                case PlayerType.Knight:
                    player.type = PlayerType.Knight;
                    player.hp = 100;
                    player.attack = 10;
                    break;
                case PlayerType.Archer:
                    player.type = PlayerType.Archer;
                    player.hp = 75;
                    player.attack = 12;
                    break;
                case PlayerType.Mage:
                    player.type = PlayerType.Mage;
                    player.hp = 50;
                    player.attack = 15;
                    break;
            }

            return player;
        }

        // 마을에 접속하는 함수
        static bool EnterTown(ref Player player)
        {
            if (player.hp <= 0)
                FullHp(ref player);

            while (true)
            {
                Console.WriteLine("마을에 접속했습니다.");
                Console.WriteLine("[1] 필드로 가기");
                Console.WriteLine("[2] 로비로 돌아가기");
                Console.WriteLine("[3] 게임 종료");

                string input = Console.ReadLine();
                switch(input)
                {
                    case "1":
                        if (EnterField(ref player))
                            break;
                        return false;
                    case "2":
                        return true;
                    case "3":
                        return false;
                }
            }
        }

        // 필드에 접속하는 함수
        static bool EnterField(ref Player player)
        {
            while(true)
            {
                Console.WriteLine("필드에 접속했습니다.");

                Random rand = new Random();
                int randValue = rand.Next(1, 4);

                Monster monster;
                CreateMonster(randValue, out monster);

                Console.WriteLine("[1] 싸우기");
                Console.WriteLine("[2] 일정 확률로 마을로 도망가기");
                Console.WriteLine("[3] 게임 종료");

                string input = Console.ReadLine();
                switch(input)
                {
                    case "1":
                        return Fight(ref player, ref monster);
                    case "2":
                        randValue = rand.Next(0, 2);
                        if(randValue == 0)
                        {
                            Console.WriteLine("도망치는데 실패했습니다.");
                            return Fight(ref player, ref monster);
                        }
                        else
                        {
                            Console.WriteLine("도망치는데 성공했습니다.");
                            return true;
                        }
                    case "3":
                        return false;
                }
            }
        }

        // 체력을 회복하는 함수
        static void FullHp(ref Player player)
        {
            switch(player.type)
            {
                case PlayerType.Knight:
                    player.hp = 100;
                    break;
                case PlayerType.Archer:
                    player.hp = 75;
                    break;
                case PlayerType.Mage:
                    player.hp = 50;
                    break;
            }
        }

        // 몬스터를 랜덤으로 생성하는 함수
        static void CreateMonster(int randValue, out Monster monster)
        {
            monster.type = MonsterType.None;
            monster.hp = 0;
            monster.attack = 0;

            switch(randValue)
            {
                case (int)MonsterType.Slime:
                    monster.type = MonsterType.Slime;
                    monster.hp = 20;
                    monster.attack = 2;
                    Console.WriteLine("슬라임이 생성되었습니다,");
                    break;
                case (int)MonsterType.Skeleton:
                    monster.type = MonsterType.Skeleton;
                    monster.hp = 30;
                    monster.attack = 3;
                    Console.WriteLine("스켈레톤이 생성되었습니다,");
                    break;
                case (int)MonsterType.Orc:
                    monster.type = MonsterType.Orc;
                    monster.hp = 40;
                    monster.attack = 4;
                    Console.WriteLine("오크가 생성되었습니다,");
                    break;
            }
        }

        // 몬스터와 싸우는 함수
        static bool Fight(ref Player player, ref Monster monster)
        {
            Console.WriteLine("전투를 시작합니다.");
            while(true)
            {
                // 플레이어가 몬스터 공격
                monster.hp -= player.attack;
                if(monster.hp <= 0)
                {
                    Console.WriteLine("몬스터를 처치했습니다.");
                    Console.WriteLine($"남은 체력 : {player.hp}");
                    return true;
                }

                // 몬스터가 플레이어 공격
                player.hp -= monster.attack;
                if (player.hp <= 0)
                {
                    Console.WriteLine("몬스터에 의해 사망했습니다.");
                    return false;
                }
            }
        }

    }

}
```
