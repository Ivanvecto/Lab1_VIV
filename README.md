#README.md

    using System.Text.RegularExpressions;
    using Serilog;
    
    class Program
    {
        static void Main()
        {
            // Конфигурация логгера
            Log.Logger = new LoggerConfiguration()
                .WriteTo.Console()  // Логирование в консоль
                .WriteTo.File("log.txt", rollingInterval: RollingInterval.Day)  // Логирование в файл (ежедневное вращение)
                .CreateLogger();
    
            Log.Information("Программа запущена");
    
            Console.WriteLine("Введите логин:");
            string login = Console.ReadLine();
    
            Console.WriteLine("Введите пароль:");
            string password = Console.ReadLine();
    
            Console.WriteLine("Подтвердите пароль:");
            string confirmPassword = Console.ReadLine();
    
            bool result = RegisterUser(login, password, confirmPassword, out string message);
    
            // Если программа успешно завершила работу, добавим лог об этом
            if (result)
            {
                Log.Information("Программа успешно завершила работу");
            }
            else
            {
                Log.Information("Программа завершила работу с ошибками");
            }
    
            // Завершение работы логгера
            Log.CloseAndFlush();
        }
    
    
        static bool RegisterUser(string login, string password, string confirmPassword, out string message)
        {
            message = "";
    
            Log.Information("Регистрация пользователя с логином: {Login}", login);
    
            // Проверка на длину логина
            if (login.Length < 5)
            {
                message = "Логин должен содержать минимум 5 символов.";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            // Проверка на валидный телефон или email или строку символов
            if (!IsValidPhone(login) && !IsValidEmail(login) && !IsValidStringLogin(login))
            {
                message = "Логин должен быть в формате телефона, email или строки символов.";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            // Проверка на совпадение с предустановленными логинами
            List<string> preinstalledLogins = new List<string>
            {
                "user1",
                "user2",
                "user3"
            };
    
            if (preinstalledLogins.Contains(login))
            {
                message = "Логин уже занят.";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            // Проверка на совпадение пароля и подтверждения пароля
            if (password != confirmPassword)
            {
                message = "Пароль и подтверждение пароля не совпадают.";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            // Проверка на длину пароля
            if (password.Length < 7)
            {
                message = "Пароль должен содержать минимум 7 символов.";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            // Проверка на соответствие пароля требованиям
            if (!IsValidPassword(password))
            {
                message = "Пароль не соответствует требованиям (минимум одна буква в верхнем и нижнем регистре, одна цифра и один спецсимвол).";
                Log.Error("Ошибка регистрации: {ErrorMessage}", message);
                return false;
            }
    
            return true;
        }
    
        static bool IsValidPhone(string input)
        {
            // Регулярное выражение для проверки телефона в формате +x-xxx-xxx-xxxx
            string pattern = @"^\+\d{1,3}-\d{3}-\d{3}-\d{4}$";
            return Regex.IsMatch(input, pattern);
        }
    
        {
            // Регулярное выражение для проверки email
            string pattern = @"^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$";
            return Regex.IsMatch(input, pattern);
        }
    
        static bool IsValidStringLogin(string input)
        {
            // Регулярное выражение для проверки строки символов
            string pattern = @"^[a-zA-Z0-9_]+$";
            return Regex.IsMatch(input, pattern);
        }
    
        static bool IsValidPassword(string input)
        {
            // Регулярное выражение для проверки пароля
            string pattern = @"^(?=.*[a-zа-я])(?=.*[A-ZА-Я])(?=.*\d)(?=.*[@#$%^&+=]).{7,}$";
            return Regex.IsMatch(input, pattern);
        }
    }
    
