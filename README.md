[laba2.py](https://github.com/user-attachments/files/32818171/laba2.py)
users = [
    {
        "login": "Bohdan",
        "password": "1234",
        "grades": [2,6,4,7,10]
    },
    {
        "login": "Vasil",
        "password": "1111",
        "grades": [5,6,12,9,10]
    },
    {
        "login": "Petro",
        "password": "2222",
        "grades": [12,10,6,6,9]
    },
    {
        "login": "Ivan",
        "password": "3333",
        "grades": [11,5,4,3,7,10]
    },
]
corrent_user = None
login = input("ведіть логін: ")
pasword = input("ведіть пароль:")
for user in users:
    if user["login"] == login and user["password"] == pasword:
        corrent_user = user
        break
if corrent_user is None:
    print("Помилка")
else:
    print("Ваші оцінки:")
    for grade in user["grades"]:
        print(grade)
