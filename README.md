# my-wm-app
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>World Medicine Костанай</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
        
        body {
            font-family: 'Roboto', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }
        
        .time-slot {
            transition: all 0.2s ease;
        }
        
        .time-slot:hover {
            transform: translateY(-2px);
        }
        
        .note-textarea {
            resize: none;
            scrollbar-width: thin;
        }
        
        .note-textarea::-webkit-scrollbar {
            width: 5px;
        }
        
        .note-textarea::-webkit-scrollbar-thumb {
            background-color: #3b82f6;
            border-radius: 10px;
        }
        
        .calendar-day.active {
            background-color: #1d4ed8;
            color: white;
        }
        
        @media (max-width: 640px) {
            .calendar-header {
                font-size: 0.9rem;
            }
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <!-- Главный контейнер -->
    <div class="max-w-md mx-auto bg-white min-h-screen shadow-lg flex flex-col" id="app-container">
        <!-- Шапка приложения -->
        <header class="bg-blue-900 text-white p-4 shadow-md">
            <div class="flex justify-between items-center">
                <div>
                    <h1 class="text-xl font-bold">World Medicine</h1>
                    <p class="text-sm opacity-80">Костанай - Мониторинг посещений</p>
                </div>
                <div class="flex items-center space-x-2">
                    <button id="sync-btn" class="p-2 rounded-full hover:bg-blue-800 transition">
                        <i class="fas fa-sync-alt"></i>
                    </button>
                    <button id="logout-btn" class="p-2 rounded-full hover:bg-blue-800 transition">
                        <i class="fas fa-sign-out-alt"></i>
                    </button>
                </div>
            </div>
        </header>

        <!-- Экран входа -->
        <div id="login-screen" class="flex-1 flex flex-col items-center justify-center p-6">
            <div class="w-full max-w-xs">
                <div class="bg-white p-8 rounded-lg shadow-md">
                    <div class="text-center mb-6">
                        <img src="https://via.placeholder.com/100x50?text=WM+Logo" alt="World Medicine Logo" class="mx-auto mb-4">
                        <h2 class="text-2xl font-bold text-blue-900">Авторизация</h2>
                        <p class="text-gray-600">Введите ваши учетные данные</p>
                    </div>
                    <form id="login-form" class="space-y-4">
                        <div>
                            <label for="username" class="block text-sm font-medium text-gray-700 mb-1">Логин</label>
                            <input type="text" id="username" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        <div>
                            <label for="password" class="block text-sm font-medium text-gray-700 mb-1">Пароль</label>
                            <input type="password" id="password" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        <button type="submit" class="w-full bg-blue-900 text-white py-2 px-4 rounded-md hover:bg-blue-800 transition focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
                            Войти
                        </button>
                    </form>
                </div>
            </div>
        </div>

        <!-- Основной интерфейс (скрыт до входа) -->
        <div id="main-interface" class="flex-1 hidden flex flex-col">
            <!-- Навигация -->
            <nav class="bg-blue-800 text-white flex justify-around">
                <button class="tab-btn py-3 px-4 flex-1 text-center font-medium border-b-2 border-transparent hover:bg-blue-700 transition active" data-tab="calendar">
                    <i class="fas fa-calendar-alt mr-2"></i>Календарь
                </button>
                <button class="tab-btn py-3 px-4 flex-1 text-center font-medium border-b-2 border-transparent hover:bg-blue-700 transition" data-tab="notes">
                    <i class="fas fa-clipboard-list mr-2"></i>Заметки
                </button>
                <button class="tab-btn py-3 px-4 flex-1 text-center font-medium border-b-2 border-transparent hover:bg-blue-700 transition" data-tab="profile">
                    <i class="fas fa-user mr-2"></i>Профиль
                </button>
            </nav>

            <!-- Контент вкладок -->
            <div class="flex-1 overflow-y-auto">
                <!-- Вкладка Календарь -->
                <div id="calendar-tab" class="tab-content p-4">
                    <div class="mb-4 flex justify-between items-center">
                        <h2 class="text-xl font-bold text-blue-900">Планирование посещений</h2>
                        <button id="add-visit-btn" class="bg-blue-900 text-white px-3 py-1 rounded-md text-sm hover:bg-blue-800 transition">
                            <i class="fas fa-plus mr-1"></i>Добавить
                        </button>
                    </div>
                    
                    <!-- Месячный календарь -->
                    <div class="mb-6 bg-white rounded-lg shadow p-2">
                        <div class="flex justify-between items-center mb-2">
                            <button id="prev-month" class="p-1 rounded-full hover:bg-gray-100">
                                <i class="fas fa-chevron-left"></i>
                            </button>
                            <h3 id="current-month" class="font-semibold">Июнь 2023</h3>
                            <button id="next-month" class="p-1 rounded-full hover:bg-gray-100">
                                <i class="fas fa-chevron-right"></i>
                            </button>
                        </div>
                        <div class="grid grid-cols-7 gap-1 text-center text-xs font-medium mb-1">
                            <div class="py-1">Пн</div>
                            <div class="py-1">Вт</div>
                            <div class="py-1">Ср</div>
                            <div class="py-1">Чт</div>
                            <div class="py-1">Пт</div>
                            <div class="py-1">Сб</div>
                            <div class="py-1">Вс</div>
                        </div>
                        <div id="month-days" class="grid grid-cols-7 gap-1 text-center">
                            <!-- Дни месяца будут добавлены через JS -->
                        </div>
                    </div>
                    
                    <!-- Расписание на день -->
                    <div class="mb-4">
                        <h3 id="current-day" class="font-semibold mb-2 text-blue-900">Сегодня, 15 июня</h3>
                        <div id="day-schedule" class="space-y-2">
                            <!-- Временные слоты будут добавлены через JS -->
                        </div>
                    </div>
                </div>

                <!-- Вкладка Заметки -->
                <div id="notes-tab" class="tab-content hidden p-4">
                    <div class="mb-4 flex justify-between items-center">
                        <h2 class="text-xl font-bold text-blue-900">Мои заметки</h2>
                        <button id="add-note-btn" class="bg-blue-900 text-white px-3 py-1 rounded-md text-sm hover:bg-blue-800 transition">
                            <i class="fas fa-plus mr-1"></i>Добавить
                        </button>
                    </div>
                    
                    <div id="notes-list" class="space-y-3">
                        <!-- Заметки будут добавлены через JS -->
                    </div>
                </div>

                <!-- Вкладка Профиль -->
                <div id="profile-tab" class="tab-content hidden p-4">
                    <div class="bg-white rounded-lg shadow p-6 mb-4">
                        <div class="flex items-center mb-4">
                            <div class="w-16 h-16 rounded-full bg-blue-100 flex items-center justify-center text-blue-900 text-2xl font-bold mr-4">
                                ИИ
                            </div>
                            <div>
                                <h2 class="text-xl font-bold" id="profile-name">Иван Иванов</h2>
                                <p class="text-gray-600" id="profile-position">Медицинский представитель</p>
                            </div>
                        </div>
                        
                        <div class="space-y-3">
                            <div>
                                <p class="text-sm text-gray-500">Телефон</p>
                                <p id="profile-phone">+7 777 123 4567</p>
                            </div>
                            <div>
                                <p class="text-sm text-gray-500">Email</p>
                                <p id="profile-email">ivanov@worldmedicine.kz</p>
                            </div>
                            <div>
                                <p class="text-sm text-gray-500">Регион</p>
                                <p id="profile-region">Костанай</p>
                            </div>
                        </div>
                    </div>
                    
                    <div class="bg-white rounded-lg shadow p-4 mb-4">
                        <h3 class="font-semibold mb-2 text-blue-900">Статистика</h3>
                        <div class="grid grid-cols-2 gap-4">
                            <div class="bg-blue-50 p-3 rounded-lg">
                                <p class="text-sm text-blue-900">Посещений сегодня</p>
                                <p class="text-xl font-bold" id="visits-today">3</p>
                            </div>
                            <div class="bg-blue-50 p-3 rounded-lg">
                                <p class="text-sm text-blue-900">Всего за месяц</p>
                                <p class="text-xl font-bold" id="visits-month">42</p>
                            </div>
                        </div>
                    </div>
                    
                    <button id="settings-btn" class="w-full bg-white text-blue-900 py-2 px-4 rounded-md border border-gray-200 hover:bg-gray-50 transition mt-4 flex items-center justify-center">
                        <i class="fas fa-cog mr-2"></i> Настройки
                    </button>
                </div>
            </div>
        </div>

        <!-- Модальное окно добавления посещения -->
        <div id="visit-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden">
            <div class="bg-white rounded-lg shadow-xl w-full max-w-md">
                <div class="p-4 border-b border-gray-200 flex justify-between items-center">
                    <h3 class="text-lg font-semibold text-blue-900">Добавить посещение</h3>
                    <button id="close-visit-modal" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <div class="p-4">
                    <form id="visit-form" class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Тип посещения</label>
                            <div class="flex space-x-2">
                                <label class="flex-1">
                                    <input type="radio" name="visit-type" value="doctor" class="peer hidden" checked>
                                    <div class="p-2 border border-gray-300 rounded-md text-center peer-checked:border-blue-500 peer-checked:bg-blue-50 peer-checked:text-blue-900 cursor-pointer">
                                        Врач
                                    </div>
                                </label>
                                <label class="flex-1">
                                    <input type="radio" name="visit-type" value="pharmacy" class="peer hidden">
                                    <div class="p-2 border border-gray-300 rounded-md text-center peer-checked:border-blue-500 peer-checked:bg-blue-50 peer-checked:text-blue-900 cursor-pointer">
                                        Аптека
                                    </div>
                                </label>
                            </div>
                        </div>
                        
                        <div>
                            <label for="visit-name" class="block text-sm font-medium text-gray-700 mb-1">Имя врача/Название аптеки</label>
                            <input type="text" id="visit-name" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        
                        <div>
                            <label for="visit-date" class="block text-sm font-medium text-gray-700 mb-1">Дата</label>
                            <input type="date" id="visit-date" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        
                        <div>
                            <label for="visit-time" class="block text-sm font-medium text-gray-700 mb-1">Время</label>
                            <input type="time" id="visit-time" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        
                        <div>
                            <label for="visit-notes" class="block text-sm font-medium text-gray-700 mb-1">Заметки</label>
                            <textarea id="visit-notes" rows="3" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 note-textarea"></textarea>
                        </div>
                    </form>
                </div>
                <div class="p-4 border-t border-gray-200 flex justify-end space-x-2">
                    <button id="cancel-visit" class="px-4 py-2 border border-gray-300 rounded-md text-gray-700 hover:bg-gray-50 transition">
                        Отмена
                    </button>
                    <button id="save-visit" class="px-4 py-2 bg-blue-900 text-white rounded-md hover:bg-blue-800 transition">
                        Сохранить
                    </button>
                </div>
            </div>
        </div>

        <!-- Модальное окно добавления заметки -->
        <div id="note-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden">
            <div class="bg-white rounded-lg shadow-xl w-full max-w-md">
                <div class="p-4 border-b border-gray-200 flex justify-between items-center">
                    <h3 class="text-lg font-semibold text-blue-900">Новая заметка</h3>
                    <button id="close-note-modal" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
                <div class="p-4">
                    <form id="note-form" class="space-y-4">
                        <div>
                            <label for="note-title" class="block text-sm font-medium text-gray-700 mb-1">Заголовок</label>
                            <input type="text" id="note-title" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                        </div>
                        
                        <div>
                            <label for="note-content" class="block text-sm font-medium text-gray-700 mb-1">Содержание</label>
                            <textarea id="note-content" rows="5" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 note-textarea"></textarea>
                        </div>
                    </form>
                </div>
                <div class="p-4 border-t border-gray-200 flex justify-end space-x-2">
                    <button id="cancel-note" class="px-4 py-2 border border-gray-300 rounded-md text-gray-700 hover:bg-gray-50 transition">
                        Отмена
                    </button>
                    <button id="save-note" class="px-4 py-2 bg-blue-900 text-white rounded-md hover:bg-blue-800 transition">
                        Сохранить
                    </button>
                </div>
            </div>
        </div>

        <!-- Уведомление -->
        <div id="notification" class="fixed bottom-4 left-1/2 transform -translate-x-1/2 bg-blue-900 text-white px-4 py-2 rounded-md shadow-lg transition-all duration-300 opacity-0 translate-y-4">
            Сохранено успешно!
        </div>
    </div>

    <script>
        // Текущий пользователь и данные
        let currentUser = null;
        let visits = [];
        let notes = [];
        let currentDate = new Date();
        
        // DOM элементы
        const loginScreen = document.getElementById('login-screen');
        const mainInterface = document.getElementById('main-interface');
        const loginForm = document.getElementById('login-form');
        const logoutBtn = document.getElementById('logout-btn');
        const syncBtn = document.getElementById('sync-btn');
        const tabBtns = document.querySelectorAll('.tab-btn');
        const tabContents = document.querySelectorAll('.tab-content');
        const addVisitBtn = document.getElementById('add-visit-btn');
        const visitModal = document.getElementById('visit-modal');
        const closeVisitModal = document.getElementById('close-visit-modal');
        const cancelVisit = document.getElementById('cancel-visit');
        const saveVisit = document.getElementById('save-visit');
        const visitForm = document.getElementById('visit-form');
        const addNoteBtn = document.getElementById('add-note-btn');
        const noteModal = document.getElementById('note-modal');
        const closeNoteModal = document.getElementById('close-note-modal');
        const cancelNote = document.getElementById('cancel-note');
        const saveNote = document.getElementById('save-note');
        const noteForm = document.getElementById('note-form');
        const notification = document.getElementById('notification');
        const currentMonthEl = document.getElementById('current-month');
        const monthDaysEl = document.getElementById('month-days');
        const currentDayEl = document.getElementById('current-day');
        const dayScheduleEl = document.getElementById('day-schedule');
        const prevMonthBtn = document.getElementById('prev-month');
        const nextMonthBtn = document.getElementById('next-month');
        const notesListEl = document.getElementById('notes-list');
        
        // Инициализация приложения
        document.addEventListener('DOMContentLoaded', () => {
            // Проверяем, есть ли сохраненный пользователь
            const savedUser = localStorage.getItem('wm_user');
            if (savedUser) {
                currentUser = JSON.parse(savedUser);
                loginScreen.classList.add('hidden');
                mainInterface.classList.remove('hidden');
                updateProfileInfo();
                loadData();
                renderCalendar();
                renderDaySchedule();
                renderNotes();
            }
            
            // Настройка событий
            setupEventListeners();
        });
        
        // Настройка обработчиков событий
        function setupEventListeners() {
            // Форма входа
            loginForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const username = document.getElementById('username').value;
                const password = document.getElementById('password').value;
                
                // Простая проверка (в реальном приложении нужно проверять на сервере)
                if (username && password) {
                    currentUser = {
                        id: Date.now().toString(),
                        name: username === 'admin' ? 'Администратор' : 'Медицинский представитель',
                        position: username === 'admin' ? 'Администратор' : 'Медицинский представитель',
                        phone: '+7 777 123 4567',
                        email: username + '@worldmedicine.kz',
                        region: 'Костанай'
                    };
                    
                    localStorage.setItem('wm_user', JSON.stringify(currentUser));
                    loginScreen.classList.add('hidden');
                    mainInterface.classList.remove('hidden');
                    updateProfileInfo();
                    loadData();
                    renderCalendar();
                    renderDaySchedule();
                    renderNotes();
                }
            });
            
            // Выход из системы
            logoutBtn.addEventListener('click', () => {
                currentUser = null;
                localStorage.removeItem('wm_user');
                localStorage.removeItem('wm_visits');
                localStorage.removeItem('wm_notes');
                loginScreen.classList.remove('hidden');
                mainInterface.classList.add('hidden');
                document.getElementById('username').value = '';
                document.getElementById('password').value = '';
            });
            
            // Синхронизация данных
            syncBtn.addEventListener('click', () => {
                showNotification('Данные синхронизированы');
                // В реальном приложении здесь бы отправлялись данные на сервер
            });
            
            // Переключение вкладок
            tabBtns.forEach(btn => {
                btn.addEventListener('click', () => {
                    const tab = btn.getAttribute('data-tab');
                    
                    // Обновляем активные кнопки
                    tabBtns.forEach(b => b.classList.remove('active', 'border-blue-500'));
                    btn.classList.add('active', 'border-blue-500');
                    
                    // Показываем соответствующую вкладку
                    tabContents.forEach(content => content.classList.add('hidden'));
                    document.getElementById(`${tab}-tab`).classList.remove('hidden');
                });
            });
            
            // Добавление посещения
            addVisitBtn.addEventListener('click', () => {
                document.getElementById('visit-date').valueAsDate = currentDate;
                visitModal.classList.remove('hidden');
            });
            
            // Закрытие модального окна посещения
            closeVisitModal.addEventListener('click', () => visitModal.classList.add('hidden'));
            cancelVisit.addEventListener('click', () => visitModal.classList.add('hidden'));
            
            // Сохранение посещения
            saveVisit.addEventListener('click', () => {
                const type = document.querySelector('input[name="visit-type"]:checked').value;
                const name = document.getElementById('visit-name').value;
                const date = document.getElementById('visit-date').value;
                const time = document.getElementById('visit-time').value;
                const notes = document.getElementById('visit-notes').value;
                
                if (name && date && time) {
                    const visit = {
                        id: Date.now().toString(),
                        type,
                        name,
                        date,
                        time,
                        notes,
                        completed: false,
                        userId: currentUser.id
                    };
                    
                    visits.push(visit);
                    saveData();
                    renderCalendar();
                    renderDaySchedule();
                    visitModal.classList.add('hidden');
                    visitForm.reset();
                    showNotification('Посещение добавлено');
                }
            });
            
            // Добавление заметки
            addNoteBtn.addEventListener('click', () => {
                noteModal.classList.remove('hidden');
            });
            
            // Закрытие модального окна заметки
            closeNoteModal.addEventListener('click', () => noteModal.classList.add('hidden'));
            cancelNote.addEventListener('click', () => noteModal.classList.add('hidden'));
            
            // Сохранение заметки
            saveNote.addEventListener('click', () => {
                const title = document.getElementById('note-title').value;
                const content = document.getElementById('note-content').value;
                
                if (title && content) {
                    const note = {
                        id: Date.now().toString(),
                        title,
                        content,
                        date: new Date().toISOString(),
                        userId: currentUser.id
                    };
                    
                    notes.push(note);
                    saveData();
                    renderNotes();
                    noteModal.classList.add('hidden');
                    noteForm.reset();
                    showNotification('Заметка сохранена');
                }
            });
            
            // Навигация по месяцам
            prevMonthBtn.addEventListener('click', () => {
                currentDate.setMonth(currentDate.getMonth() - 1);
                renderCalendar();
            });
            
            nextMonthBtn.addEventListener('click', () => {
                currentDate.setMonth(currentDate.getMonth() + 1);
                renderCalendar();
            });
        }
        
        // Обновление информации профиля
        function updateProfileInfo() {
            if (!currentUser) return;
            
            document.getElementById('profile-name').textContent = currentUser.name;
            document.getElementById('profile-position').textContent = currentUser.position;
            document.getElementById('profile-phone').textContent = currentUser.phone;
            document.getElementById('profile-email').textContent = currentUser.email;
            document.getElementById('profile-region').textContent = currentUser.region;
            
            // Обновляем статистику
            const today = new Date().toISOString().split('T')[0];
            const visitsToday = visits.filter(v => v.date === today).length;
            const currentMonth = new Date().getMonth();
            const visitsThisMonth = visits.filter(v => {
                const visitMonth = new Date(v.date).getMonth();
                return visitMonth === currentMonth;
            }).length;
            
            document.getElementById('visits-today').textContent = visitsToday;
            document.getElementById('visits-month').textContent = visitsThisMonth;
        }
        
        // Загрузка данных
        function loadData() {
            const savedVisits = localStorage.getItem('wm_visits');
            const savedNotes = localStorage.getItem('wm_notes');
            
            visits = savedVisits ? JSON.parse(savedVisits) : [];
            notes = savedNotes ? JSON.parse(savedNotes) : [];
            
            // Фильтруем данные по текущему пользователю
            if (currentUser) {
                visits = visits.filter(v => v.userId === currentUser.id);
                notes = notes.filter(n => n.userId === currentUser.id);
            }
        }
        
        // Сохранение данных
        function saveData() {
            localStorage.setItem('wm_visits', JSON.stringify(visits));
            localStorage.setItem('wm_notes', JSON.stringify(notes));
            updateProfileInfo();
        }
        
        // Рендер календаря
        function renderCalendar() {
            const year = currentDate.getFullYear();
            const month = currentDate.getMonth();
            
            // Обновляем заголовок месяца
            const monthNames = ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 
                               'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'];
            currentMonthEl.textContent = `${monthNames[month]} ${year}`;
            
            // Получаем первый день месяца и количество дней в месяце
            const firstDay = new Date(year, month, 1).getDay();
            const daysInMonth = new Date(year, month + 1, 0).getDate();
            
            // Корректируем первый день (воскресенье = 0, понедельник = 1)
            const startDay = firstDay === 0 ? 6 : firstDay - 1;
            
            // Очищаем контейнер дней
            monthDaysEl.innerHTML = '';
            
            // Добавляем пустые ячейки для дней предыдущего месяца
            for (let i = 0; i < startDay; i++) {
                const emptyCell = document.createElement('div');
                emptyCell.className = 'py-2 text-gray-400';
                monthDaysEl.appendChild(emptyCell);
            }
            
            // Добавляем дни месяца
            const today = new Date();
            const isCurrentMonth = today.getFullYear() === year && today.getMonth() === month;
            
            for (let day = 1; day <= daysInMonth; day++) {
                const dayCell = document.createElement('div');
                dayCell.className = 'py-2 cursor-pointer rounded-full calendar-day';
                
                // Проверяем, есть ли посещения в этот день
                const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
                const dayVisits = visits.filter(v => v.date === dateStr);
                
                if (dayVisits.length > 0) {
                    dayCell.classList.add('font-bold');
                    
                    // Добавляем индикатор посещений
                    const indicator = document.createElement('div');
                    indicator.className = 'w-1 h-1 mx-auto mt-1 rounded-full bg-blue-500';
                    dayCell.appendChild(document.createTextNode(day));
                    dayCell.appendChild(indicator);
                } else {
                    dayCell.textContent = day;
                }
                
                // Выделяем текущий день
                if (isCurrentMonth && day === today.getDate()) {
                    dayCell.classList.add('active');
                }
                
                // Выделяем выбранный день
                if (currentDate.getDate() === day && currentDate.getMonth() === month && currentDate.getFullYear() === year) {
                    dayCell.classList.add('active');
                }
                
                // Обработчик клика по дню
                dayCell.addEventListener('click', () => {
                    currentDate = new Date(year, month, day);
                    renderCalendar();
                    renderDaySchedule();
                });
                
                monthDaysEl.appendChild(dayCell);
            }
        }
        
        // Рендер расписания на день
        function renderDaySchedule() {
            const dayNames = ['Воскресенье', 'Понедельник', 'Вторник', 'Среда', 'Четверг', 'Пятница', 'Суббота'];
            const monthNames = ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня', 
                               'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря'];
            
            const dayName = dayNames[currentDate.getDay()];
            const dayNum = currentDate.getDate();
            const monthName = monthNames[currentDate.getMonth()];
            const year = currentDate.getFullYear();
            
            // Форматируем дату для сравнения
            const dateStr = currentDate.toISOString().split('T')[0];
            
            // Обновляем заголовок дня
            const today = new Date();
            if (currentDate.toDateString() === today.toDateString()) {
                currentDayEl.textContent = `Сегодня, ${dayNum} ${monthName}`;
            } else {
                currentDayEl.textContent = `${dayName}, ${dayNum} ${monthName} ${year}`;
            }
            
            // Получаем посещения на этот день
            const dayVisits = visits.filter(v => v.date === dateStr)
                                   .sort((a, b) => a.time.localeCompare(b.time));
            
            // Очищаем расписание
            dayScheduleEl.innerHTML = '';
            
            if (dayVisits.length === 0) {
                const emptyMsg = document.createElement('div');
                emptyMsg.className = 'text-center py-4 text-gray-500';
                emptyMsg.textContent = 'Нет запланированных посещений';
                dayScheduleEl.appendChild(emptyMsg);
                return;
            }
            
            // Добавляем посещения
            dayVisits.forEach(visit => {
                const visitEl = document.createElement('div');
                visitEl.className = 'bg-white rounded-lg shadow p-3 time-slot border-l-4';
                visitEl.style.borderLeftColor = visit.type === 'doctor' ? '#3b82f6' : '#10b981';
                
                const time = visit.time.substring(0, 5);
                
                visitEl.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <div class="font-semibold">${visit.name}</div>
                            <div class="text-sm text-gray-600">${time} • ${visit.type === 'doctor' ? 'Врач' : 'Аптека'}</div>
                        </div>
                        <div class="flex space-x-2">
                            <button class="complete-btn p-1 text-gray-400 hover:text-blue-500" data-id="${visit.id}">
                                <i class="fas ${visit.completed ? 'fa-check-circle text-green-500' : 'fa-circle'}"></i>
                            </button>
                            <button class="delete-btn p-1 text-gray-400 hover:text-red-500" data-id="${visit.id}">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </div>
                    ${visit.notes ? `<div class="mt-2 text-sm text-gray-700">${visit.notes}</div>` : ''}
                `;
                
                dayScheduleEl.appendChild(visitEl);
            });
            
            // Добавляем обработчики для кнопок
            document.querySelectorAll('.complete-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    e.stopPropagation();
                    const id = btn.getAttribute('data-id');
                    const visit = visits.find(v => v.id === id);
                    if (visit) {
                        visit.completed = !visit.completed;
                        saveData();
                        renderDaySchedule();
                        showNotification(visit.completed ? 'Посещение отмечено как выполненное' : 'Посещение отмечено как невыполненное');
                    }
                });
            });
            
            document.querySelectorAll('.delete-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    e.stopPropagation();
                    if (confirm('Удалить это посещение?')) {
                        const id = btn.getAttribute('data-id');
                        visits = visits.filter(v => v.id !== id);
                        saveData();
                        renderCalendar();
                        renderDaySchedule();
                        showNotification('Посещение удалено');
                    }
                });
            });
        }
        
        // Рендер заметок
        function renderNotes() {
            notesListEl.innerHTML = '';
            
            if (notes.length === 0) {
                const emptyMsg = document.createElement('div');
                emptyMsg.className = 'text-center py-4 text-gray-500';
                emptyMsg.textContent = 'Нет сохраненных заметок';
                notesListEl.appendChild(emptyMsg);
                return;
            }
            
            // Сортируем заметки по дате (новые сначала)
            const sortedNotes = [...notes].sort((a, b) => new Date(b.date) - new Date(a.date));
            
            sortedNotes.forEach(note => {
                const noteEl = document.createElement('div');
                noteEl.className = 'bg-white rounded-lg shadow p-4';
                
                const date = new Date(note.date);
                const formattedDate = date.toLocaleDateString('ru-RU', {
                    day: 'numeric',
                    month: 'long',
                    year: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit'
                });
                
                noteEl.innerHTML = `
                    <div class="flex justify-between items-start mb-2">
                        <h3 class="font-semibold text-blue-900">${note.title}</h3>
                        <button class="delete-note-btn p-1 text-gray-400 hover:text-red-500" data-id="${note.id}">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                    <p class="text-gray-700 mb-2">${note.content}</p>
                    <div class="text-xs text-gray-500">${formattedDate}</div>
                `;
                
                notesListEl.appendChild(noteEl);
            });
            
            // Добавляем обработчики для кнопок удаления
            document.querySelectorAll('.delete-note-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    if (confirm('Удалить эту заметку?')) {
                        const id = btn.getAttribute('data-id');
                        notes = notes.filter(n => n.id !== id);
                        saveData();
                        renderNotes();
                        showNotification('Заметка удалена');
                    }
                });
            });
        }
        
        // Показать уведомление
        function showNotification(message) {
            notification.textContent = message;
            notification.classList.remove('opacity-0', 'translate-y-4');
            
            setTimeout(() => {
                notification.classList.add('opacity-0', 'translate-y-4');
            }, 3000);
        }
        
        // Имитация синхронизации между устройствами
        window.addEventListener('storage', (e) => {
            if (e.key === 'wm_visits' || e.key === 'wm_notes') {
                loadData();
                renderCalendar();
                renderDaySchedule();
                renderNotes();
                showNotification('Данные обновлены');
            }
        });
    </script>
</body>
</html>
