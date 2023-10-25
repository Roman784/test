![Титульник](https://github.com/Roman784/test/blob/main/TLab5.jpg)

**Цель:** получить практические навыки в создании программ,
содержащих статические методы, и изучить поведение членов с
модификатором static.

**Постановка задачи**\
Изучить соответствующий материал. В данной работе необходимо
изучить определение статических методов, их правильное применение,
проверить на практике различие в поведении статических и не статических
сущностей программы.


```C#
using DataAccess;
using System;

namespace Main
{
    public static class PersonsData
    {
        private static List<Person> persons = new List<Person>();

        public static void AddPerson(Person person)
        {
            persons.Add(person);
        }

        public static Person GetPerson(int id)
        {
            foreach (Person person in persons)
            {
                if (person.Id == id) return person;
            }

            Console.WriteLine("Человек не найден");
            return null;
        }
    }

    public class Person
    {
        private static int idCounter;
        public int Id { get; private set; }

        private string firstName = "";
        private string surname = "";
        private int birthDate;

        public string FirstName
        {
            get { return firstName; }
            set { firstName = value; }
        }

        public string Surname
        {
            get { return surname; }
            set { surname = value; }
        }

        public int BirthDate
        {
            get { return birthDate; }
            set { birthDate = Math.Clamp(value, 1900, 2023); }
        }

        public int Age { get => 2023 - BirthDate; }

        public Person(string firstName, string surname, int birthDate)
        {
            Id = idCounter;
            idCounter += 1;

            FirstName = firstName;
            Surname = surname;
            BirthDate = birthDate;

            PersonsData.AddPerson(this);
        }

        public void Show()
        {
            Console.WriteLine($"ID: {Id}");
            Console.WriteLine($"Имя: {FirstName}");
            Console.WriteLine($"Фамилия: {Surname}");
            Console.WriteLine($"Возраст: {Age}");
            Console.WriteLine($"Дата рождения: {BirthDate}\n");
        }
    }

    public class Student : Person
    {
        private int gradebookNumber = 0;
        private float gradePointAverage = 0f;

        public int GradebookNumber
        {
            get { return gradebookNumber; }
            set { gradebookNumber = Math.Clamp(value, 0, value); }
        }

        public float GradePointAverage
        {
            get { return gradePointAverage; }
            set { gradePointAverage = Math.Clamp(value, 0f, 100f); }
        }

        public Student(string firstName, string surname, int birthDate, float gradePointAverage) : base(firstName, surname, birthDate)
        {
            GradePointAverage = gradePointAverage;
        }

        public new void Show()
        {
            Console.WriteLine($"Имя: {FirstName}");
            Console.WriteLine($"Фамилия: {Surname}");
            Console.WriteLine($"Возраст: {Age}");
            Console.WriteLine($"Дата рождения: {BirthDate}");
            Console.WriteLine($"Средний балл: {GradePointAverage}");
            Console.WriteLine($"Номер зачётки: {GradebookNumber}\n");
        }
    }

    public class University
    {
        private List<Student> students = new List<Student>();

        private int gradebookNumberCounter = 0;

        public List<Student> GetStudents() => new List<Student>(students);
        public void SetStudents(List<Student> newStudents)
        {
            students = new List<Student>(newStudents);

            // Поиск последнего(наибольшего) номера в зачётке.
            foreach (Student student in students)
            {
                if (student.GradebookNumber >= gradebookNumberCounter)
                    gradebookNumberCounter = student.GradebookNumber + 1;
            }
        }

        public void AddStudent(Student newStudent)
        {
            if (newStudent == null)
                throw new NullReferenceException("Попытка добавить несуществующего студента");

            newStudent.GradebookNumber = gradebookNumberCounter;
            gradebookNumberCounter += 1;

            students.Add(newStudent);
        }

        public void RemoveStudent(Student student)
        {
            if (student == null)
                throw new NullReferenceException("Попытка удалить несуществующего студента");

            students.Remove(student);
        }

        public Student GetStudentByName(string firstName, string surname)
        {
            foreach (Student student in students)
            {
                if (student.FirstName == firstName && student.Surname == surname)
                    return student;
            }

            Console.WriteLine("Студент не найден\n");
            return null;
        }

        public Student GetStudentByGradebookNumber(int number)
        {
            foreach (Student student in students)
            {
                if (student.GradebookNumber == number)
                    return student;
            }

            Console.WriteLine("Студент не найден\n");
            return null;
        }

        public void ShowAllStudents()
        {
            foreach (Student student in students)
            {
                student.Show();
            }
        }
    }

    public class Program
    {
        static void Main(string[] args)
        {
            University university = new University();

            Student student1 = new Student("1", "1", 9999, -1f);
            Student student2 = new Student("2", "2", -1, 200f);
            Student student3 = new Student("3", "3", 1, 1f);

            university.AddStudent(student1);
            university.AddStudent(student2);
            university.AddStudent(student3);

            university.ShowAllStudents();

            Console.WriteLine("---------------- Удаление студента ----------------\n");

            university.RemoveStudent(student2);

            university.ShowAllStudents();

            Console.WriteLine("----------- Получение студента по имени -----------\n");

            Student student = university.GetStudentByName("3", "3");
            student?.Show();

            student = university.GetStudentByName("3", "2"); // Несуществующий студент.
            student?.Show();

            Console.WriteLine("------ Сохранение и загрузка списка студентов -----\n");

            IDataSerializer serializer = new JSONSerializer(); // Способ, которым будут сериализоваться данные.
            Repository repository = new StudentsRepository(serializer); // StudentsRepository ограничивает работу, допускается только List<Student>.

            repository.Save(university.GetStudents());
            List<Student> students = (List<Student>)repository.Load();

            University university2 = new University();
            university2.SetStudents(students);
            university2.ShowAllStudents();

            Console.WriteLine("----------------- Список людей -----------------\n");

            PersonsData.GetPerson(0)?.Show();
            PersonsData.GetPerson(1)?.Show();
            PersonsData.GetPerson(2)?.Show();
        }
    }
}
```
```C#
using Main;
using System.Text.Json;
using System.Text.Json.Serialization;

namespace DataAccess
{
    // Отвечает за способы сохранения/чтения данных, будь это JSON, XML и тд. 
    public interface IDataSerializer
    {
        public void Save(Object data, string path);
        public Object Load<T>(string path);
    }

    public class JSONSerializer : IDataSerializer
    {
        private string fileExtension = ".json";
        private string fullPath = ""; // Путь к файлу с учётом расширения.

        private JsonSerializerOptions options = new()
        {
            ReferenceHandler = ReferenceHandler.IgnoreCycles,
            WriteIndented = true
        };

        public void Save(Object data, string path)
        {
            try
            {
                string json = JsonSerializer.Serialize(data, options);

                fullPath = path + fileExtension; 
                StreamWriter file = new StreamWriter(fullPath, false);
                file.Write(json);
                file.Close();

                Console.WriteLine("Файл сохранён через JSON");
                Console.WriteLine($"Путь к файлу {fullPath}\n");
            }
            catch { Console.WriteLine("Ошибка сохранения"); }
        }

        public Object Load<T>(string path)
        {
            fullPath = path + fileExtension;

            if (!File.Exists(fullPath))
            {
                Console.WriteLine("Файл для загрузки не найден");
                return null;
            }

            try
            {
                string json = File.ReadAllText(fullPath);
                return JsonSerializer.Deserialize<T>(json);
            }
            catch
            {
                Console.WriteLine("Ошибка загрузки");
                return null;
            }
        }
    }

    public class Repository
    {
        protected IDataSerializer serializer = new JSONSerializer();
        protected string savePath = "";

        public Repository(string saveFileName, IDataSerializer serializer)
        {
            savePath = Path.Combine(Environment.CurrentDirectory, saveFileName);
            this.serializer = serializer;
        }

        public void Save(Object data) => serializer.Save(data, savePath);
        public virtual Object Load() => serializer.Load<Object>(savePath);
    }

    public class StudentsRepository : Repository
    {
        private static string saveFileName = "students";

        public StudentsRepository(IDataSerializer serializer) : base(saveFileName, serializer) { }

        public override List<Student> Load()
        {
            Object data = serializer.Load<List<Student>>(savePath);

            if (data == null) return new List<Student>();
            return (List<Student>)data;
        }
    }
}
```

![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2012.png)
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2013.png)
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2014.png)

В класс человека были перенесены некоторые поля из студента: имя и дата рождения, а также был добавлен id и статический счётчик для него.
Устанавливается id в конструкторе, здесь же экземпляр человека добавляется в общий список людей.
```C#
private static int idCounter;
public int Id { get; private set; }

public Person(string firstName, string surname, int birthDate)
{
    Id = idCounter;
    idCounter += 1;

    FirstName = firstName;
    Surname = surname;
    BirthDate = birthDate;

    PersonsData.AddPerson(this);
}
```

Статический класс PersonData содержит список людей, методы для их добавления, и поиска конкретного по id.
```C#
public static class PersonsData
{
    private static List<Person> persons = new List<Person>();

    public static void AddPerson(Person person)
    {
        persons.Add(person);
    }

    public static Person GetPerson(int id)
    {
        foreach (Person person in persons)
        {
            if (person.Id == id) return person;
        }

        Console.WriteLine("Человек не найден");
        return null;
    }
}
```

Теперь при добавлении студента в университет, ему присваивается уникальный, в данном экземпляре вуза, номер зачётной книжки.
Также, устанавливая новый список студентов, счётчик для номеров зачёток ориентрируется на максимальный номер.
```C#
private int gradebookNumberCounter = 0;

public void AddStudent(Student newStudent)
{
    if (newStudent == null)
        throw new NullReferenceException("Попытка добавить несуществующего студента");

    newStudent.GradebookNumber = gradebookNumberCounter;
    gradebookNumberCounter += 1;

    students.Add(newStudent);
}

public void SetStudents(List<Student> newStudents)
{
    students = new List<Student>(newStudents);

    // Поиск последнего(наибольшего) номера в зачётке.
    foreach (Student student in students)
    {
        if (student.GradebookNumber >= gradebookNumberCounter)
            gradebookNumberCounter = student.GradebookNumber + 1;
    }
}
```

Пример демонстрации работы дополнился выводом информации о людях по их id. Можно было бы создать метод с циклом в PersonData, но я решил выводить по одному.
```C#
Console.WriteLine("----------------- Список людей -----------------\n");
PersonsData.GetPerson(0)?.Show();
PersonsData.GetPerson(1)?.Show();
PersonsData.GetPerson(2)?.Show();
```
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2016.png)

Методы Show у Person и Student.
```C#
// У Person
public void Show()
{
    Console.WriteLine($"ID: {Id}");
    Console.WriteLine($"Имя: {FirstName}");
    Console.WriteLine($"Фамилия: {Surname}");
    Console.WriteLine($"Возраст: {Age}");
    Console.WriteLine($"Дата рождения: {BirthDate}\n");
}

// У Student
public new void Show()
{
    Console.WriteLine($"Имя: {FirstName}");
    Console.WriteLine($"Фамилия: {Surname}");
    Console.WriteLine($"Возраст: {Age}");
    Console.WriteLine($"Дата рождения: {BirthDate}");
    Console.WriteLine($"Средний балл: {GradePointAverage}");
    Console.WriteLine($"Номер зачётки: {GradebookNumber}\n");
}
```

Код и логика сохранения не изменились.
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2015.png)
![Скриншот](https://github.com/Roman784/test/blob/main/Screenshot_2017.png)
