![титульник](https://github.com/Mospolytech-IIT/oop-labs-2023-II-lab-2-DisVic/assets/124364123/bf11378a-8a20-4373-9a4d-2adbf52ccfb8)

Лабораторная работа №2 "Структуры. Типы значений и ссылочные типы"

Цель: получить практические навыки в создании программ, содержащих добавление в программу структур. Изучить разницы в поведении типов значений и ссылочных типов. Изучить работу сборщика мусора, а также работы сборщика мусора с использованием пользовательских типов данных.

Листинг:
```
using System;

namespace Playlist
{
    class LAB_2
    {
        public struct Song
        {
            public string songName;
            public int yearOfRelease;
            public string bandName;
            public string genre;

            public Song(string songName, int yearOfRelease, string bandName, string genre)
            {
                this.songName = songName;
                this.yearOfRelease = yearOfRelease;
                this.bandName = bandName;
                this.genre = genre;
            }

            public Song() { }
            public Song(string newSong):this(newSong,2000," "," ") { }
            public void ViewSongInformation()
            {
                Console.WriteLine($"Группа {bandName} - композиция {songName}. Год выхода: {yearOfRelease}. Жанр: {genre} \n");
            }
            public void ChangeSongInformation(string newSongName, int newYearOfRelease, string newBandName, string newGenre)
            {
                songName = newSongName;
                yearOfRelease = newYearOfRelease;
                bandName = newBandName;
                genre = newGenre;
            }
            public Song CopyStruct(string newSongName, string newNameOfBand)=> this with { songName = newSongName, bandName = newNameOfBand};
        }
        public class Playlist
        {
            List<Song> favoriteSongs;
            public Song soundtrack;

            public Playlist(Song soundtrack, List<Song> favoriteSongs)
            {
                this.favoriteSongs = favoriteSongs;
                this.soundtrack = soundtrack;
            }
            public void AddSong(Song soundtrack) => favoriteSongs.Add(soundtrack);

            public void ShowPlaylist() 
            {
                foreach (Song sound in favoriteSongs)
                {
                    Console.WriteLine($"Трек из плейлиста: {sound.songName} от группы: {sound.bandName}. Жанр: {sound.genre}. Год выхода: {sound.yearOfRelease} \n");
                }
                Console.WriteLine("Конец плейлиста \n");        
            }

            public Playlist CopyClass(Song newSound, List<Song> newfavoriteSongs)=> new Playlist(newSound, newfavoriteSongs);
        }
        public static void Main(string[] args)
        {
            Song song = new Song("Miracle", 2022, "A Day To Remember", "Metal");
            Song _song= song.CopyStruct("Reaching out","Fit for a king");
            Song sng = _song;
            Song s = new Song("Safe and sound");
            s.ViewSongInformation();
            s.ChangeSongInformation("Hypa Hypa", 2019, "Electric callboy", "Electrocore");
            sng.ViewSongInformation();
            sng.ChangeSongInformation("Venom", 2018, "Eminem", "Kamikaze");
            song.ViewSongInformation();
            _song.ViewSongInformation();
            sng.ViewSongInformation();
            s.ViewSongInformation();

            Console.WriteLine("Копирование классов без метода\n");

            List<Song> songs = new List<Song>();

            Playlist playlist = new Playlist(song, songs);
            playlist.AddSong(song);
            Playlist _playlist= playlist;
            _playlist.AddSong(_song);
            playlist.AddSong(s); 
            playlist.ShowPlaylist();
            _playlist.ShowPlaylist();

            Console.WriteLine("Копирование классов с методом\n");

            List<Song> newSongs = new List<Song>();
            List<Song> newSongs1 = new List<Song>();

            Playlist pl=new Playlist(song, newSongs);
            pl.AddSong(song);
            Playlist _pl = pl.CopyClass(sng, newSongs1);
            _pl.AddSong(s);
            pl.AddSong(_song);
            pl.ShowPlaylist();
            _pl.ShowPlaylist();
        }
    }
}
```
Результат программы:

![Вывод](https://github.com/Mospolytech-IIT/oop-labs-2023-II-lab-2-DisVic/assets/124364123/590f08cb-7b0b-4b3c-95b5-867fc471a08f)



В данной программе я создал структуру Song с 4 полями, тремя конструкторами,  методом вывода данных об объекте структуры (название песни, год выхода, название группы, жанр), методом изменения всех полей в структурном объекте, методом копирования структуры с изменением двух полей (название группы и название трека). Также был создан класс Playlist, полями которого являются структурный объект и список для хранения объектов типа Song. Также в классе создан конструктор, метод добавления песни в список (плэйлист), метод вывода в консоли всей информации о каждой песне, а также метод копирования класса с последующим присваиванием новых переменных в поля объекта класса.
