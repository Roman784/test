![Титульник](https://github.com/Roman784/test/blob/main/TLab7.png)

**Цель:** получить практические навыки в создании свойств структур и классов, вырабатываются навыки их определения, изучаются примеры возможного их
применения и использования.

**Постановка задачи**\
Изучить соответствующий материал. В данной работе изучается механизм наследования объектно-ориентированных программ, способы его применения и особенности
использования.
Создание программы с наследованием элементов. Изучение реализации взаимодействия между базовыми элементами и их наследниками. Изучение преобразование типов в наследовании.


```C#
using System;

public class Program
{
    public class Enemy
    {
        public string Name { get; set; } = "";

        private int health = 0;
        public int Health 
        { 
            get => health; 
            set
            {
                if (value < 0) 
                    throw new ArgumentOutOfRangeException("Значение не может быть отрицательным");
                health = value;
            }
        }

        private int damage = 0;
        public int Damage
        {
            get => damage;
            set
            {
                if (value < 0)
                    throw new ArgumentOutOfRangeException("Значение не может быть отрицательным");
                damage = value;
            }
        }

        public virtual string ToString()
        {
            return
                $"Имя: {Name}\n" +
                $"Здоровье: {Health}\n" +
                $"Урон: {Damage}\n";
        }
    }

    public class Spider : Enemy
    {
        private float moveSpeed = 0;
        public float MoveSpeed
        {
            get => moveSpeed;
            set
            {
                if (value < 0)
                    throw new ArgumentOutOfRangeException("Значение не может быть отрицательным");
                moveSpeed = value;
            }
        }

        public override string ToString()
        {
            return
                base.ToString() +
                $"Скорость передвижения: {MoveSpeed}\n";
        }

        public void Move()
        {
            Console.WriteLine($"{Name} переместился со скоростью {MoveSpeed}");
        }
    }

    public class Tower : Enemy
    {
        private float shotsCooldown = 0;
        public float ShotsCooldown
        {
            get => shotsCooldown;
            set
            {
                if (value < 0)
                    throw new ArgumentOutOfRangeException("Значение не может быть отрицательным");
                shotsCooldown = value;
            }
        }

        public override string ToString()
        {
            return
                base.ToString() +
                $"Задержка между выстрелами: {ShotsCooldown}\n";
        }

        public void Shoot()
        {
            Console.WriteLine($"{Name} выстрелила");
        }
    }

    public class EnemiesPool
    {
        private List<Spider> spiders = new List<Spider>();
        private List<Tower> towers = new List<Tower>();

        public void Add(Enemy enemy)
        {
            if (enemy is Spider)
                spiders.Add((Spider)enemy);

            if (enemy is Tower)
                towers.Add((Tower)enemy);
        }

        public int GetSpidersCount() => spiders.Count;
        public int GetTowersCount() => towers.Count;

        public string GetStatistics()
        {
            return
                $"Количество пауков: {GetSpidersCount()}\n" +
                $"Количество башен: {GetTowersCount()}\n";
        }
    }

    static void Main(string[] args)
    {
        EnemiesPool pool = new EnemiesPool();

        Enemy enemy1 = new Enemy();
        Enemy enemy2 = new Enemy();

        Spider spider1 = new Spider() { Name = "паук1", Health = 1, Damage = 1, MoveSpeed = 1 };
        Spider spider2 = new Spider() { Name = "паук2", Health = 2, Damage = 2, MoveSpeed = 2 };

        Tower tower1 = new Tower() { Name = "башня1", Health = 1, Damage = 1, ShotsCooldown = 1 };
        Tower tower2 = new Tower() { Name = "башня2", Health = 2, Damage = 2, ShotsCooldown = 2 };

        Console.WriteLine(enemy1.ToString());
        Console.WriteLine(enemy2.ToString());

        Console.WriteLine(spider1.ToString());
        Console.WriteLine(spider2.ToString());

        Console.WriteLine(tower1.ToString());
        Console.WriteLine(tower2.ToString());

        spider1.Move();
        spider2.Move();

        tower1.Shoot();
        tower2.Shoot();

        Console.WriteLine();

        pool.Add(spider1);
        pool.Add(spider2);
        pool.Add(tower1);
        pool.Add(tower2);

        Console.WriteLine(pool.GetStatistics());
    }
}
```

Базовый класс Enemy имеет 3 свойства: Name, Health и Damage. 
У дочернего Spider добавляется функционал для передвижения - свойство MoveSpeed и метод Move. 
Противник типа Tower, в свою очередь, двигаться не способен, вместо этого он стреляет с некой задержкой - свойство ShotsCooldown и метод Shoot.

Для хранения всех врагов реализован класс EnemiesPool.

---

```C#
Enemy enemy1 = new Enemy();
Enemy enemy2 = new Enemy();

Spider spider1 = new Spider() { Name = "паук1", Health = 1, Damage = 1, MoveSpeed = 1 };
Spider spider2 = new Spider() { Name = "паук2", Health = 2, Damage = 2, MoveSpeed = 2 };

Tower tower1 = new Tower() { Name = "башня1", Health = 1, Damage = 1, ShotsCooldown = 1 };
Tower tower2 = new Tower() { Name = "башня2", Health = 2, Damage = 2, ShotsCooldown = 2 };

Console.WriteLine(enemy1.ToString());
Console.WriteLine(enemy2.ToString());

Console.WriteLine(spider1.ToString());
Console.WriteLine(spider2.ToString());

Console.WriteLine(tower1.ToString());
Console.WriteLine(tower2.ToString());
```
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2026.png)

---

```C#
spider1.Move();
spider2.Move();

tower1.Shoot();
tower2.Shoot();
```
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2027.png)

---

```C#
pool.Add(spider1);
pool.Add(spider2);
pool.Add(tower1);
pool.Add(tower2);

Console.WriteLine(pool.GetStatistics());
```
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2028.png)
