![Титульник](https://github.com/Roman784/test/blob/main/TLab8.png)

**Цель:** получить практические навыки в создании свойств структур и классов, вырабатываются навыки их определения, изучаются примеры возможного их
применения и использования.

**Постановка задачи**\
Изучить соответствующий материал. В данной работе изучается механизм
применения абстрактных классов, вырабатываются навыки использования таких
конструкций. Нарабатывается способы написания программ с использованием методики SOLID.


```C#
using System;

public class Program
{
    public abstract class Device
    {
        public abstract string Type { get; }

        public abstract void TurnOn();
        public abstract void TurnOff();
    }

    public class Computer : Device
    {
        public override string Type { get => "Компьютер"; }
        public string ScreenResolution { get; private set; } = "";

        public Computer(string screenResolution)
        {
            ScreenResolution = screenResolution;
        } 

        public override void TurnOn()
        {
            Console.WriteLine("Компьютер включен");
        }

        public override void TurnOff()
        {
            Console.WriteLine("Компьютер выключен");
        }

        public void OpenProgram(string program)
        {
            Console.WriteLine($"Открытие приложения {program}");
        }
    }

    public class Printer : Device
    {
        public override string Type { get => "Принтер"; }
        public int CartridgeCount { get; private set; }

        public Printer(int cartridgeCount)
        {
            CartridgeCount = cartridgeCount;
        }

        public override void TurnOn()
        {
            Console.WriteLine("Принтер включен");
        }

        public override void TurnOff()
        {
            Console.WriteLine("Принтер выключен");
        }

        public void Print(string document)
        {
            Console.WriteLine($"Печать документа {document}");
        }
    }

    public class VacuumCleaner : Device
    {
        public override string Type { get => "Пылесос"; }
        public float Power { get; private set; }

        public VacuumCleaner(float power)
        {
            Power = power;
        }

        public override void TurnOn()
        {
            Console.WriteLine("Пылесос включен");
        }

        public override void TurnOff()
        {
            Console.WriteLine("Пылесос выключен");
        }

        public void StartСleaning()
        {
            Console.WriteLine($"Старт уборки");
        }
    }

    public static void F(Device device)
    {
        Console.WriteLine($"Работа с {device.Type}");
        device.TurnOn();
        device.TurnOff();

        Console.WriteLine();
    }

    static void Main(string[] args)
    {
        Computer computer = new Computer("1920x1080");
        Printer printer = new Printer(4);
        VacuumCleaner vacuumCleaner = new VacuumCleaner(300);

        F(computer);
        F(printer);
        F(vacuumCleaner);

        computer.OpenProgram("Блокнот");
        printer.Print("Курсовая.doc");
        vacuumCleaner.StartСleaning();
    }
}
```

![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2032.png)

Абстрактный класс Device определяет свойство с типом устройства, а также методы для его включения/выключения.
Каждый из производных классов реализует их по своему, для демонстрации методы просто выводят сообщение в консоль.
