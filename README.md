
API Frontpad
Подготовка к подключению интернет-магазина
1. Включите API в разделе "Общие" настройки.
2. В разделе "Общие" настройки получите и сохраните секретный код (далее секрет).
3. Присвойте товарам уникальный цифровой артикул в карточке каждого товара. Код необходимо присваивать только тем товарам, которые будут продаваться в интернет-магазине.
Отправка запроса
Для отправки запроса необходимо использовать метод POST — эквивалент html form submit.
Адрес для передачи параметров зависит от метода https://app.frontpad.ru/api/index.php?МЕТОД
Кодировка uft-8
Обратите внимание, количество обращений ограничено 30 в минуту.
Методы (возможности)
Название
Метод
URL запроса
Отправка заказа
new_order
https://app.frontpad.ru/api/index.php?new_order
Получение статуса заказа
get_status
https://app.frontpad.ru/api/index.php?get_status
Получение информации о клиенте
get_client
https://app.frontpad.ru/api/index.php?get_client
Получение информации о сертификате
get_certificate
https://app.frontpad.ru/api/index.php?get_certificate
Получение списока товаров
get_products
https://app.frontpad.ru/api/index.php?get_products

Ответы API общие для всех методов (в формате JSON)
Параметр
Значение
Описание
result
success
Успешное завершение
result
error
Ошибка
error
invalid_secret
Неверный секрет


requests_limit
Превышено количество обращение к API


api_off
API выключен


invalid_plant
На текущем тарифе API недоступен

Отправка заказа
Метод предназначен для передачи заказа из интернет-магазина или приложения.
 
Метод: new_order
URL запроса: https://app.frontpad.ru/api/index.php?new_order
Параметры запроса:
	secret - секрет;
        	product – массив артикулов товаров;
        	product_kol – массив количества товаров;
product_mod – массив модификаторов товаров, где значение элемента массива является ключом родителя (товара в который добавлен модификатор);
product_price – массив цен товаров (установка цены при заказе через API возможна только для товаров с включенной опцией "Изменение цены при создании заказа";
       	score – баллы для оплаты заказа;	
sale – скидка, положительное, целое число от 1 до 100;
sale_amount - скидка суммой, назначить к заказу можно один тип скидки - процентную или суммой;
card – карта клиента, положительное, целое число до 16 знаков;
street – улица, длина до 50 знаков;
home – дом, длина до 50 знаков;
pod – подъезд, длина до 2 знаков;
et – этаж, длина до 2 знаков;
apart – квартира, длина до 50 знаков;
phone – телефон, длина до 50 знаков;
mail – адрес электронной почты, длина до 50 знаков, доступно только с активной опцией автоматического сохранения клиентов;
descr – примечание, длина до 100 знаков;
name – имя клиента, длина до 50 знаков;
pay – отметка оплаты заказа, значение можно посмотреть в справочнике “Варианты оплаты”;
certificate – номер сертификата;
person – количество персон, длина 2 знака. Обратите внимание, привязка "автосписания" к количеству персон, переданному через api, не осуществляется;
channel – канал продаж, значение можно посмотреть в справочнике программы;
datetime – время “предзаказа”, указывается в формате ГГГГ-ММ-ДД ЧЧ:ММ:СС, 
например 2016-08-15 15:30:00. Максимальный период предзаказа - 30 дней от текущей даты;
affiliate – филиал, значение можно посмотреть в справочнике программы;
point – точка продаж, значение можно посмотреть в справочнике программы.
 
Ответ API (успешный):
        	result: success;
        	order_id: id заказа;
order_number: номер заказа, соответствующий номеру в программе;

Пример ответа:
{
   "result": "success",
   "order_id": "1000020",
   "order_number": "143"
}
 
Ответ API (ошибка):
        	result: error;
        	error: cash_close - смена закрыта;
error: invalid_product_keys - неверный массив товаров;
Пример ответа:
{
   "result": "error",
   "error": "cash_close",
}

Ответ API (предупреждения):
warnings: invalid_certificate - неверный или несуществующий номер сертификата;
warnings: invalid_datetime - неверный формат даты предзаказа (invalid_format) 
или неверный период предзаказа (too_long_period);
warnings: invalid_product_keys - массив неверных или несуществующих артикулов товаров, где значение элемента массива invalid_product_keys является ключом элемента массива product. В примере неверными являются товары с ключами 1 и 5 из массива product;
Пример ответа:
{
   "result": "success",
   "order_id": "1000020",
   "warnings": {
       "invalid_product_keys": {
           "0": "1",
           "1": "5"
        }
    }
}
 


Проверка статуса заказа
Метод предназначен для проверки статуса заказа, форма проверки может быть установлена в личном кабинете интернет-магазина. Запрещается циклическая проверка статуса. Статус может быть проверен по id заказа или по номеру телефона. В случае проверки по номеру телефона поиск заказов осуществляется только в текущий день.
 
Метод: get_status
 
URL запроса https://app.frontpad.ru/api/index.php?get_status
 
Параметры запроса:
secret - секрет;
order_id – id заказа, полученный в методе new_order
или
client_phone – номер телефона клиента. Обратите внимание, осуществляется строгий поиск номера, используйте маску в программе и в интернет-магазине.
 
Ответ API (успешный):
        	result: success;
        	status: название статуса.
Пример ответа:
{
   "result": "success",
   "status": "В производстве",
}
 
Ответ API (ошибка):
        	общие для всех методов ответы при ошибке описаны выше;
        	result: error;
        	error: invalid_order_id – неверный или несуществующий id заказа;
        	error: invalid_client_phone - неверный или несуществующий номер телефона.
Пример ответа:
{
   "result": "error",
   "status": "invalid_order_id"
}
 
Информация о клиенте
Метод предназначен для получения информации о клиенте, форма проверки может быть установлена в личном кабинете интернет-магазина. Запрещается циклический поиск клиентов.
 
Метод: get_client
URL запроса: https://app.frontpad.ru/api/index.php?get_client
Параметры запроса:
secret - секрет;
client_phone – номер телефона клиента. Обратите внимание, осуществляется строгий поиск номера, используйте маску в программе и в интернет-магазине.
 
Ответ API (успешный):
        	result: success;
name: имя;
street: улица;
home: дом;
pod: подъезд;
et: этаж;
apart: квартира;
mail: электронная почта;
descr: примечание;
card: карта клиента;
sale: скидка клиента;
score: лицевой счет (баллы).
 Пример ответа:
{
   "result": "success",
   "name": "Иван",
   "street": "Мира",
   "home": "17",
   "pod": "1",
   "et": "2",
   "apart": "6",
   "mail": "mail@mail.com",
   "descr": "",
   "card": "4321",
   "sale": "10",
   "score": "100".
}

Ответ API (ошибка):
        	общие для всех методов ответы при ошибке описаны выше;
        	result: error;
        	error: invalid_client_phone - неверный или несуществующий номер телефона.
Пример ответа:
{
   "result": "error",
   "error": "invalid_client_phone"
}
 

Проверка сертификата
Метод предназначен для проверки сертификата, форма проверки может быть установлена в личном кабинете интернет-магазина. Запрещается циклическая проверка сертификата.
 
Метод: get_certificate
URL запроса https://app.frontpad.ru/api/index.php?get_certificate
Параметры запроса:
secret - секрет;
certificate – номер сертификата.
 
Ответ API (успешный):
        	result: success;
 
Если сертификат на товар:
product_id: артикул товара;
name: название товара;
price: цена товара;
 
Если сертификат на скидку:
sale: скидка;
 
Если сертификат на скидку суммой:
amount: скидка суммой.
Пример ответа:
{
   "result": "success",
   "product_id": "0001",
   "name": "Филадельфия",
   "price": "250"
}
 
Ответ API (ошибка):
        	общие для всех методов ответы при ошибке описаны выше;
        	result: error;
        	error: invalid_certificate – неверный или несуществующий номер сертификата.
Пример ответа:
{
   "result": "error",
   "error": "invalid_certificate"
}

Список товаров
Метод предназначен для получения списка товаров и синхронизации их с интернет-магазином. Выгружаются только товары, имеющие артикул.
 
Метод: get_products
URL запроса: https://app.frontpad.ru/api/index.php?get_products
Параметры запроса:
secret - секрет.
 
Ответ API (успешный):
        	result: success;
product_id: массив артикулов товаров;
name: массив имен товаров;
price: массив цен товаров.
 Пример ответа:
{
   "result": "success",
   "product_id": {
       "0": "001",
       "1": "002"
    },
   "name": {
       "0": "Филадельфия",
       "1": "Калифорния"
    },
   "price": {
       "0": "250",
       "1": "350"
    }
}

Ответ API (ошибка):
        	общие для всех методов ответы при ошибке описаны выше;
        	result: error;
        	error: invalid_products - отсутствуют товары для выгрузки.
Пример ответа:
 	{
   "result": "error",
   "error": "invalid_products"
}
