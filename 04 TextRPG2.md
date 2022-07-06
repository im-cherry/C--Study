# 04 TextRPG2

### Creature.cs

```c#
using System;

namespace CSharp
{
    public enum CreatureType
    {
        None = 0,
        Player = 1,
        Monster = 2
    }

    class Creature
    {
        CreatureType type = CreatureType.None;
        protected int hp = 0;
        protected int attack = 0;

        protected Creature(CreatureType type)
        {
            this.type = type;
        }

        public void SetInfo(int hp, int attack)
        {
            this.hp = hp;
            this.attack = attack;
        }

        public int GetHp() { return this.hp; }
        public int GetAttack() { return this.attack; }

        public bool IsDead() { return this.hp <= 0; }
        public void onDamaged(int damage)
        {
            this.hp -= damage;
            if (this.hp <= 0)
                this.hp = 0;
        }
    }
}

```

---

### Player.cs

```c#
using System;

namespace CSharp
{
    public enum PlayerType
    {
        None = 0,
        Knight = 1,
        Archer = 2,
        Mage = 3
    }

    class Player : Creature
    {
        protected PlayerType type;

        protected Player(PlayerType type) : base(CreatureType.Player)
        {
            this.type = type;
        }

        public PlayerType GetPlayerType() { return this.type; }
    }

    class Knight : Player
    {
        public Knight() : base(PlayerType.Knight)
        {
            SetInfo(100, 10);
        }
    }

    class Archer : Player
    {
        public Archer() : base(PlayerType.Archer)
        {
            SetInfo(75, 12);
        }
    }

    class Mage : Player
    {
        public Mage() : base(PlayerType.Mage)
        {
            SetInfo(50, 15);
        }
    }
}

```

---

### Monster.cs

```c#
using System;

namespace CSharp
{
    public enum MonsterType
    {
        None = 0,
        Slime = 1,
        Skeleton = 2,
        Orc = 3
    }

    class Monster : Creature
    {
        protected MonsterType type;

        protected Monster(MonsterType type) : base(CreatureType.Monster)
        {
            this.type = type;
        }

        public MonsterType GetMonsterType() { return this.type; }
    }

    class Slime : Monster
    {
        public Slime() : base(MonsterType.Slime)
        {
            SetInfo(20, 2);
        }
    }

    class Skeleton : Monster
    {
        public Skeleton() : base(MonsterType.Skeleton)
        {
            SetInfo(30, 3);
        }
    }

    class Orc : Monster
    {
        public Orc() : base(MonsterType.Orc)
        {
            SetInfo(40, 4);
        }
    }
}

```

---

### Game.cs

```c#
using System;

namespace CSharp
{
    public enum GameMode
    {
        None = 0,
        Lobby = 1,
        Town = 2,
        Field = 3
    }

    class Game
    {
        GameMode mode = GameMode.Lobby;
        Player player;
        Monster monster;

        Random rand = new Random();

        public bool Process()
        {
            bool isExit = false;

            switch(mode)
            {
                case GameMode.Lobby:
                    ProcessLobby();
                    break;
                case GameMode.Town:
                    ProcessTown();
                    break;
                case GameMode.Field:
                    ProcessField();
                    break;
                case GameMode.None:
                    isExit = true;
                    break;
            }

            return isExit;
        }

        public void ProcessLobby()
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
                    player = new Knight();
                    mode = GameMode.Town;
                    break;
                case "2":
                    player = new Archer();
                    mode = GameMode.Town;
                    break;
                case "3":
                    player = new Mage();
                    mode = GameMode.Town;
                    break;
                case "4":
                    mode = GameMode.None;
                    break;
            }
        }

        public void ProcessTown()
        {
            Console.WriteLine("마을에 입장했습니다.");
            Console.WriteLine("[1] 필드로 가기");
            Console.WriteLine("[2] 로비로 돌아가기");
            Console.WriteLine("[3] 게임 종료");

            string input = Console.ReadLine();
            switch (input)
            {
                case "1":
                    mode = GameMode.Field;
                    break;
                case "2":
                    mode = GameMode.Lobby;
                    break;
                case "3":
                    mode = GameMode.None;
                    break;
            }
        }

        public void ProcessField()
        {
            Console.WriteLine("필드에 입장했습니다.");
            Console.WriteLine("[1] 몬스터와 싸우기");
            Console.WriteLine("[2] 일정 확률로 마을로 도망가기");
            Console.WriteLine("[3] 게임 종료");

            CreateRandomMonster();

            string input = Console.ReadLine();
            switch (input)
            {
                case "1":
                    ProcessFight();
                    break;
                case "2":
                    TryEscape();
                    break;
                case "3":
                    mode = GameMode.None;
                    break;
            }
        }

        public void CreateRandomMonster()
        {
            int randValue = rand.Next(1, 4);
            switch(randValue)
            {
                case (int)MonsterType.Slime:
                    monster = new Slime();
                    Console.WriteLine("슬라임이 생성되었습니다!");
                    break;
                case (int)MonsterType.Skeleton:
                    monster = new Skeleton();
                    Console.WriteLine("스켈레톤이 생성되었습니다!");
                    break;
                case (int)MonsterType.Orc:
                    monster = new Orc();
                    Console.WriteLine("오크가 생성되었습니다!");
                    break;
            }
        }

        public void ProcessFight()
        {
            Console.WriteLine("몬스터와 싸움을 시작합니다.");

           while(true)
            {
                int damage = player.GetAttack();
                monster.onDamaged(damage);
                if(monster.IsDead())
                {
                    Console.WriteLine("몬스터를 처치했습니다.");
                    Console.WriteLine($"남은 체력 : {player.GetHp()}");
                    break;
                }

                damage = monster.GetAttack();
                player.onDamaged(damage);
                if(player.IsDead())
                {
                    Console.WriteLine("몬스터에 의해 사망했습니다.");
                    mode = GameMode.Lobby;
                    break;
                }
            }
        }

        public void TryEscape()
        {
            int randValue = rand.Next(0, 2);

            if(randValue == 0)
            {
                mode = GameMode.Town;
                Console.WriteLine("마을로 도망치는데 성공했습니다.");
            }
            else
            {
                Console.WriteLine("마을로 도망치는데 실패했습니다.");
                ProcessFight();
            }
        }
    }
}

```

---

### Program.cs

```c#
using System;

namespace CSharp
{
    class Program
    {
        static void Main(string[] args)
        {
            Game game = new Game();

            Console.WriteLine("게임을 시작합니다.\n");
            while(true)
            {
                bool isExit = game.Process();

                if (isExit)
                    break;
            }
            Console.WriteLine("\n게임을 종료합니다.");
        }
    }
}
```
