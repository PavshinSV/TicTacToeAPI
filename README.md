# TicTacToeAPI

## API работает на порту 5001(5000), как работает:

В программе реализован контроллер "Game" со следующими методами:
1. `login` - принимает на вход два параметра email и passwword для верификации игрока. Выдает результат в виде объекта ActionResult со строковым значением.
2. `status` - принимает на вход один параметр token. Выдает результат в виде объекта ActionResult со строковым значением.
3. `new-game` - принимает на вход один параметр token. Выдает результат в виде объекта ActionResult со строковым значением.
4. `get-move` - принимает на вход три параметра: `token`, `raw`, `column`. Выдает результат в виде объекта ActionResult со строковым значением.


## Подробное описание каждого метода:

### `login`

Принимает на вход два текстовых поля `email` и `password` для идентификации пользователя

https://localhost:7261/api/Game/login?email=someEmail%40here.zone&password=somePassword

В случае если для ползователя с email хэш совпадает с хэшем от поля password - метод возвращет значение `ActionResult` `Ok` со строковой переменной `token` которая используется для выполнения хода или запроса текущего статуса пользователя

В случае если хэш в базе данных не совпадает с хэшем принятого пароля возвращается значение `ActionResult` `BadRequest` со строковым значением `Incorrect Password`

В случае если пользователь с `email` отстутствует в базе данных создается новый пользователь с указанным `password` и возвращется значение `ActionResult` `Ok` со строковой переменной `token`

### status

принимает на вход один параметр типа строка - `token`

https://localhost:7261/api/Game/status?token=47e2b833b5ffd410972e3c844604ac99a74d0e43b4b5403da96af1d41cd0972e

В случае если в БД обнаружится игровое поле для игрока с принятым `token` вернется значение `ActionResult` `Ok` со строкой json содержащей информацию о текущем статусе игры : `{"Token":"47e2b833b5ffd410972e3c844604ac99a74d0e43b4b5403da96af1d41cd0972e","Field":[[0,0,0],[0,0,0],[0,0,0]],"Status":2}`

* **Token** - текущий токен игрока;
* **Field** - двумерный массив, игровое поле. Значение ячейки массива - целое число (0, 1, 2):
    
    **0** - ячейка пустая;

    **1** - установлен маркер игрока;

    **2** - установлен маркер компьютера;

* **Status** - текущий статус игры;

    **0** - выиграл игрок;

    **1** - выиграл компьютер;

    **2** - ожидание хода игрока;
    
    **3** - ничья;

Если данных об игровом поле игрока имеющего `token` не будут обнаружены будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Game Not Found`

### new-game

принимает на вход один параметр типа строка - `token`

https://localhost:7261/api/Game/new-game?token=47e2b833b5ffd410972e3c844604ac99a74d0e43b4b5403da96af1d41cd0972e

Если данных об игровом поле игрока имеющего `token` не будет обнаружено - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Game Not Found`

Если игровое поле игрока имеющего `token` будет обнаружено - игра будет сброшена в начальное состояние. Значение всех ячеек поля `Field` будут равны `0`, а статус игры станет равным `2` - ожидание хода игрока, также будет возвращено состояние текущей игры - аналогично методу `status`

### get-move

принимает на вход три параметра: `token`, `raw`, `column`.

https://localhost:7261/api/Game/get-move?token=47e2b833b5ffd410972e3c844604ac99a74d0e43b4b5403da96af1d41cd0972e&raw=1&column=2

**token** - если игра со значением `token` не обнаружена - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `You must log in`

**raw** - номер строки игрового поля куда игрок планирует установить маркер (счет начинаем с нуля, максимальное значение - размер игрового поля минус 1)

**column** - номер столбца игрового поля куда игрок планирует установить маркер (счет начинаем с нуля, максимальное значение - размер игрового поля минус 1)

* если значения `raw` и `column` больше размера игрового поля (по умолчанию игровое поле имеет размер 3, значит максимальное значение `raw` и `column` = 2) или меньше 0 - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Wrong field size`
* если статус игры не равен 2 (ожидание хода игрока) - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Game is over. You need to start new game`
* если ячейка, в которую предполагается установить маркер занята ранее установленным маркером (иаркер игрока или маркер компьютера) - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Field is occupied`
* если по каким-либо причинам запись в БД с ходом игрока не закончится успехом (ну мало-ли) - будет выдано значение `ActionResult` `BadRequest` с текстовым сообщением `Inner Server Error. Please Try Later`, данные о ходе сохранены не будут.

если ошибок не будет обнаружено - в указанную клетку будет помещен маркер игрока, проверен статус игры и в случае если игра не окончена - будут выполнен ход компьютера. контроллер вернет текущее состояние игрового поля в виде строки json, как и в методе `status`

## Прятной Вам игры!
