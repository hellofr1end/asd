# asd


```python
import psycopg2

def create_table(conn):
    cur = conn.cursor()
    cur.execute("""
        CREATE TABLE IF NOT EXISTS films(
                id SERIAL PRIMARY KEY,
                film_name TEXT NOT NULL,
                cinema_name TEXT,
                ticketcs INTEGER NOT NULL DEFAULT 10
                );
""")
    conn.commit()
    
    


def add_film(conn):
    film = input("Введите название фильма").strip()
    cinema = input("Введите название кинотеатра").strip()
    cur = conn.cursor()
    cur.execute(
        "INSERT INTO films (film_name, cinema_name) VALUES(%s, %s)", (film, cinema)
    )
    conn.commit()
    
    print("Фильм успешно добавлен!..")



def show_films(conn):
    cur = conn.cursor()
    cur.execute("SELECT id, film_name, cinema_name, ticketcs FROM films ORDER BY id;")
    films = cur.fetchall()
    if not films:
        print("Список фильмов пусть. ")
    else:
        for film in films:
            print(f"ID: {film[0]} | Фильм: {film[1]} | Кинотеатр: {film[2]} | Кол-во билетов: {film[3]}")
    cur.close()

def buy_ticket(conn):
    film_id = input("Введите ID фильма на который хотите купить билет").strip()
    cur = conn.cursor()
    cur.execute(
        "SELECT ticketcs FROM films WHERE id = %s", (film_id,))
    result = cur.fetchone()
    if result is None:
        print("Фильм с таким id не существует")
    else:
        tickets = result[0]
        if tickets > 0:
            cur.execute("UPDATE films SET ticketcs = ticketcs -1 WHERE id = %s", (film_id,))
            conn.commit()
            print("Билет куплен успешно!")
        else:
            print("Билетов больше нет!")
        cur.close()

def menu():
    print(" ------Меню------ ")
    print("1. Добавить фильм")
    print("2. Вывести все фильмы")
    print("3. Купит билет")
    print("5. Выход")


def main():
    try:
        conn = psycopg2.connect(
            dbname='cinema',
            user='postgres',
            password='qwe',
            host='localhost',
            port='5432'
        )
    except Exception as e:
        print("не удалось подключиться к бд", e)
        return
    
    create_table(conn)

    while True:
        menu()
        choice = input("Выберете действие: ")
        if choice == '1':
            add_film(conn)
        elif choice == '2':
            show_films(conn)
        elif choice == '3':
            buy_ticket(conn)
        elif choice == '4':
            print("В доработке")
        elif choice == '5':
            print("Выход....")
            break
    #conn.close()
    print("Программа завершена")




if __name__ == '__main__':
    main()


```
