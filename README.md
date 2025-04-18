# sparta_git
스파르타 text Rpg 과제

using System;
using System.Collections.Generic;
using System.Linq;

namespace textRpg;


class Item
{
    public string   Name         { get; }
    public int      AttackBonus  { get; }
    public int      DefenseBonus { get; }
    public string   Description  { get; }
    public bool     Equipped     { get; set; }

    public Item(string name, int atk, int def, string desc, bool equipped = false)
    {
        Name         = name;
        AttackBonus  = atk;
        DefenseBonus = def;
        Description  = desc;
        Equipped     = equipped;
    }
}

class ShopEntry
{
    public string Name         { get; }
    public int    AttackBonus  { get; }
    public int    DefenseBonus { get; }
    public string Description  { get; } 
    public int    Price        { get; }

    public ShopEntry(string name, int atk, int def, string desc, int price)
    {
        Name         = name;
        AttackBonus  = atk;
        DefenseBonus = def;
        Description  = desc;
        Price        = price;
    }
}


class Character
{
    public string Name    { get; private set; }
    public string Job     { get; private set; }
    public int    Level   { get; private set; }
    public int    HP      { get; private set; }
    public int    MaxHP   { get; private set; }
    public int    Attack  { get; private set; }
    public int    Defence { get; private set; }
    public int    Gold    { get; private set; }

    private readonly int baseAttack;
    private readonly int baseDefense;

    public List<Item> Items { get; } = new List<Item>();

    public List<ShopEntry> ShopItems { get; } = new List<ShopEntry>

    {
    new ShopEntry("수련자 갑옷",   0,  5,  "수련에 도움을 주는 갑옷입니다.",              1000),
    new ShopEntry("무쇠갑옷",      0,  9,  "무쇠로 만들어져 튼튼한 갑옷입니다.",            2000),
    new ShopEntry("스파르타의 갑옷",0, 15,  "스파르타 전사들의 전설의 갑옷입니다.",        3500),
    new ShopEntry("낡은 검",      2,  0,  "쉽게 볼 수 있는 낡은 검입니다.",              600),
    new ShopEntry("청동 도끼",     5,  0,  "어디선가 사용됐던 것 같은 도끼입니다.",        1500),
    new ShopEntry("스파르타의 창", 7,  0,  "스파르타 전사들의 전설의 창입니다.",          3000),
    };

    public Character(string name, string job,
                     int level = 1, int maxHp = 100,
                     int attack = 10, int defense = 5,
                     int gold = 1500)

    {
        Name    = name;
        Job     = job;
        Level   = level;
        MaxHP   = maxHp;
        HP      = maxHp;
        Attack  = attack;
        Defence = defense;
        Gold    = gold;

        baseAttack  = attack;
        baseDefense = defense;
    }

    public void ApplyInitialEquipmentBonuses()
    {
        foreach (var it in Items)
            if  (it.Equipped)
            {
                Attack += it.AttackBonus;
                Defence += it.DefenseBonus;
            }
    }

    public void ShowStatus()
    {
        while (true)
        {
            Console.Clear();
            Console.WriteLine("==== 상태보기 ====\n");
            Console.WriteLine($"Lv. {Level:00}");
            Console.WriteLine($"{Name}( {Job} )\n");

             
            int atkBonus = Attack  - baseAttack;
            int defBonus = Defence - baseDefense;

             
            string atkSuffix = atkBonus > 0 ? $" (+{atkBonus})" : "";
            string defSuffix = defBonus > 0 ? $" (+{defBonus})" : "";

            Console.WriteLine($"공격력 : {Attack}{atkSuffix}");
            Console.WriteLine($"방어력 : {Defence}{defSuffix}");
            Console.WriteLine($"체력  : {HP}");
            Console.WriteLine($"Gold : {Gold} G\n");

            Console.WriteLine("0. 나가기");
            Console.Write("원하시는 행동을 입력해주세요.\n>> ");

            string input = Console.ReadLine()?.Trim() ?? "";
            if (input == "0")
                break;
            Console.WriteLine("\n잘못된 입력입니다. 0을 눌러 나가세요.");
            Console.ReadKey();
        }
        Console.Clear();
    }

    public void ShowInventory()
    {
        while (true)
        {
            Console.Clear();
            Console.WriteLine("==== 인벤토리 ====\n");
            Console.WriteLine("보유 중인 아이템을 관리할 수 있습니다.\n");
            Console.WriteLine("[아이템 목록]");

            if (Items.Count == 0)
            {
                Console.WriteLine("(아이템이 없습니다.)\n");
            }

            else
            {
                foreach (var item in Items)
                {
                    string mark = item.Equipped ? "[E]" : "  ";
                    Console.WriteLine($"{mark}{item.Name,-12} | " +
                                      $"공격력 +{item.AttackBonus} " +
                                      $"{(item.DefenseBonus > 0 ? $" | 방어력 +{item.DefenseBonus}" : "")}");
                                      Console.WriteLine($" {item.Description}");
                }
                Console.WriteLine();
            }
            Console.WriteLine("1. 장착 관리");
            Console.WriteLine(Items.Count == 0 ? "0. 나가기" : "2. 나가기");
            Console.Write("\n원하시는 행동을 입력해주세요.\n>> ");

            string input = Console.ReadLine()?.Trim() ?? "";

            if ((Items.Count == 0 && input == "0") ||
                (Items.Count > 0 && input == "2"))
            {
                break;
            }
            else if (input == "1")
            {
                ManageEquipment();
            }
            else
            {
                Console.WriteLine("\n잘못된 입력입니다.");
                Console.WriteLine("계속하려면 아무 키나 누르세요...");
                Console.ReadKey();
            }

        }
        Console.Clear();
    }

    public void ManageEquipment()
    {
        while (true)
        {
            Console.Clear();
            Console.WriteLine("==== 장착 관리 ====\n");
            Console.WriteLine("보유 중인 아이템을 관리할 수 있습니다.\n");
            Console.WriteLine("[아이템 목록]");

            if (Items.Count == 0)
            {
                Console.WriteLine("(장착할 아이템이 없습니다.)");
                Console.WriteLine("아무 키나 누르면 돌아갑니다...");
                Console.ReadKey();
                break;
            }

            for (int i = 0; i < Items.Count; i++)
            {
                var it = Items[i];
                string mark = it.Equipped ? "[E]" : "   ";
                Console.WriteLine(
                    $"- {i + 1}. {mark}{it.Name,-12} | " +
                    $"(공+{it.AttackBonus}, 방+{it.DefenseBonus}) | " +
                    $"{ it.Description}"
                    );
            }
            Console.WriteLine("0. 나가기");
            Console.Write("\n번호를 입력해주세요:\n>> ");

            var sel = Console.ReadLine()?.Trim() ?? "";
            if (sel == "0") break;

            if (!int.TryParse(sel, out int idx)

                || idx < 1
                || idx > Items.Count)

            {
                Console.WriteLine("\n잘못된 입력입니다.");
                Console.WriteLine("계속하려면 아무 키나 누르세요...");
                Console.ReadKey();
                continue;
            }

            var item = Items[idx - 1];
            if (item.Equipped)
            {
                item.Equipped = false;
                Attack -= item.AttackBonus;
                Defence -= item.DefenseBonus;
                Console.WriteLine($"\n{item.Name}의 장착을 해제했습니다.");
            }
            else
            {
                item.Equipped = true;
                Attack += item.AttackBonus;
                Defence += item.DefenseBonus;
                Console.WriteLine($"\n{item.Name}을(를) 장착했습니다.");
            }

            Console.WriteLine("계속하려면 아무 키나 누르세요...");
            Console.ReadKey();
        }
        Console.Clear();
    }
    public void ShowShop()
    {
        while (true)
        {
            Console.Clear();
            Console.WriteLine("==== 상점 ====\n");
            Console.WriteLine("필요한 아이템을 얻을 수 있는 상점입니다.\n");

            
            Console.WriteLine("[보유 골드]");
            Console.WriteLine($"{Gold} G\n");

            
            Console.WriteLine("[아이템 목록]");
            for (int i = 0; i < ShopItems.Count; i++)
            {
                var entry = ShopItems[i];
                bool purchased = Items.Any(it => it.Name == entry.Name);

                
                string priceOrDone = purchased
                    ? "구매완료"
                    : $"{entry.Price} G";

                
                Console.WriteLine(
                    $"- {entry.Name,-12} | " +
                    $"(공+{entry.AttackBonus}, 방+{entry.DefenseBonus}) | " +
                    $"{entry.Description,-40} | {priceOrDone}"
                );
            }

            Console.WriteLine("\n1. 아이템 구매");
            Console.WriteLine("0. 나가기");
            Console.Write("\n원하시는 행동을 입력해주세요.\n>> ");

            var sel = Console.ReadLine()?.Trim() ?? "";
            if (sel == "0") break;
            if (sel == "1") ManagePurchase();
            else
            {
                Console.WriteLine("\n잘못된 입력입니다.");
                Console.WriteLine("계속하려면 아무 키나 누르세요...");
                Console.ReadKey();
            }
        }
        Console.Clear();
    }
    private void ManagePurchase()
    {
        Console.Clear();
        Console.WriteLine("==== 아이템 구매 ====\n");
        Console.WriteLine("구매할 아이템 번호를 입력하세요. (0: 취소)\n");

        for (int i = 0; i < ShopItems.Count; i++)
        {
            var e = ShopItems[i];
            Console.WriteLine($"{i + 1}. {e.Name,-12} | {e.Price} G");
        }
        Console.WriteLine();
        Console.Write("번호 입력 >> ");

        var sel = Console.ReadLine()?.Trim() ?? "";
        if (sel == "0") return;

        if (!int.TryParse(sel, out int idx) || idx < 1 || idx > ShopItems.Count)
        {
            Console.WriteLine("\n잘못된 입력입니다.");
        }
        else
        {
            var entry = ShopItems[idx - 1];
            bool purchased = Items.Any(it => it.Name == entry.Name);

            if (purchased)
            {
                Console.WriteLine("\n이미 구매하신 아이템입니다.");
            }
            else if (Gold < entry.Price)
            {
                Console.WriteLine("\n골드가 부족합니다.");
            }
            else
            {
                Gold -= entry.Price;
                Items.Add(new Item(
                    name    : entry.Name,
                    atk     : entry.AttackBonus,
                    def     : entry.DefenseBonus,
                    desc    : entry.Description,
                    equipped: false));

                    Console.WriteLine($"\n{entry.Name}을(를) 구매했습니다!");
            }
        }

        Console.WriteLine("\n계속하려면 아무 키나 누르세요...");
        Console.ReadKey();
    }

}





class Program
{
    static void Main(string[] args)
    {

        Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다.");
        Console.WriteLine("이곳에서 던전으로 들어가기전 활동을 할 수 있습니다.\n");
        Console.WriteLine("계속하려면 아무 키나 누르세요...");
        Console.ReadKey();

        Console.Clear();


        bool isRunning   = true;
        Character player = new Character(name: "Chad", job: "전사");

        player.Items.Add(new Item("무쇠갑옷", 0, 5, "무쇠로 만들어져 튼튼한 갑옷입니다.", true));
        player.Items.Add(new Item("스파르타의 창", 7, 0, "스파르타 전사들의 전설의 창입니다.", true));
        player.Items.Add(new Item("낡은 검", 2, 0, "쉽게 볼 수 있는 낡은 검입니다.", false));

        player.ApplyInitialEquipmentBonuses();


        while (isRunning)
        {
            DisplayMenu();

            Console.Write("원하시는 행동을 입력해주세요 (0: 종료): ");
            string input = Console.ReadLine()?.Trim() ?? "";

            if(!int.TryParse(input, out int choice))
            {
                Console.WriteLine("\n잘못된 입력입니다. 숫자만 입력해주세요.");
                continue;
            }
            Console.WriteLine();

            switch (choice)
            {
                case 1:
                    player.ShowStatus();
                    break;
                case 2:
                    player.ShowInventory();
                    break;
                case 3:
                    player.ShowShop(); 
                    break;
                case 0:
                    Console.WriteLine("게임을 종료합니다.");
                    isRunning = false;
                    break;

                default:
                    Console.WriteLine("잘못된 입력입니다. 메뉴에 있는 숫자를 입력해주세요.");
                    break;
            }
            Console.WriteLine();
        }

        static void DisplayMenu()
        {
          
            Console.WriteLine("==== 스파르타 마을 ====");
            Console.WriteLine("1. 상태 보기");
            Console.WriteLine("2. 인벤토리");
            Console.WriteLine("3. 상점");
            Console.WriteLine("0. 종료");
        }
       

    }
}

