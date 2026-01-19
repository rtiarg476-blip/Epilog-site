import React, { useState, useEffect } from 'react';
import { MessageCircle, User, Phone, Send, Search, MoreVertical, ArrowLeft, Users, AlertCircle } from 'lucide-react';

const ArceusMessenger = () => {
  const [currentScreen, setCurrentScreen] = useState('phone');
  const [phoneNumber, setPhoneNumber] = useState('');
  const [countryCode, setCountryCode] = useState('+380');
  const [nickname, setNickname] = useState('');
  const [error, setError] = useState('');
  const [userData, setUserData] = useState(null);
  const [selectedChat, setSelectedChat] = useState(null);
  const [message, setMessage] = useState('');
  const [allUsers, setAllUsers] = useState([]);
  const [chats, setChats] = useState([]);
  const [messages, setMessages] = useState([]);
  const [loading, setLoading] = useState(false);

  // Инициализация - проверка сохраненного пользователя
  useEffect(() => {
    const savedUser = localStorage.getItem('arceus_user');
    if (savedUser) {
      try {
        const user = JSON.parse(savedUser);
        setUserData(user);
        setCurrentScreen('main');
        loadAllUsers();
      } catch (error) {
        console.error('Ошибка загрузки пользователя:', error);
      }
    }
  }, []);

  // Автообновление данных
  useEffect(() => {
    if (userData && currentScreen === 'main') {
      loadAllUsers();
      loadChats();
      
      const interval = setInterval(() => {
        loadAllUsers();
        loadChats();
        if (selectedChat) {
          loadChatMessages(selectedChat.chatId);
        }
      }, 5000);
      
      return () => clearInterval(interval);
    }
  }, [userData, currentScreen, selectedChat]);

  const loadAllUsers = () => {
    const usersData = localStorage.getItem('arceus_all_users');
    if (usersData) {
      try {
        const users = JSON.parse(usersData);
        setAllUsers(users.filter(u => u.phone !== userData?.phone));
      } catch (error) {
        setAllUsers([]);
      }
    }
  };

  const loadChats = () => {
    if (!userData) return;
    const chatsData = localStorage.getItem(`arceus_chats_${userData.phone}`);
    if (chatsData) {
      try {
        setChats(JSON.parse(chatsData));
      } catch (error) {
        setChats([]);
      }
    }
  };

  const loadChatMessages = (chatId) => {
    const messagesData = localStorage.getItem(`arceus_messages_${chatId}`);
    if (messagesData) {
      try {
        setMessages(JSON.parse(messagesData));
      } catch (error) {
        setMessages([]);
      }
    }
  };

  const handlePhoneSubmit = () => {
    if (phoneNumber.length < 9) {
      setError('Введите корректный номер телефона');
      return;
    }
    
    const fullPhone = countryCode + phoneNumber;
    const usersData = localStorage.getItem('arceus_all_users');
    
    if (usersData) {
      try {
        const users = JSON.parse(usersData);
        const existingUser = users.find(u => u.phone === fullPhone);
        if (existingUser) {
          setError('Этот номер уже зарегистрирован!');
          return;
        }
      } catch (error) {
        console.error('Ошибка проверки:', error);
      }
    }
    
    setError('');
    setCurrentScreen('nickname');
  };

  const handleNicknameSubmit = () => {
    if (nickname.length < 4) {
      setError('Никнейм должен содержать минимум 4 символа');
      return;
    }
    
    setLoading(true);
    setError('');
    
    const fullPhone = countryCode + phoneNumber;
    const newUser = {
      phone: fullPhone,
      nickname: nickname,
      country: countryCode === '+380' ? 'Украина' : 'Россия',
      registeredAt: new Date().toISOString()
    };

    try {
      // Сохраняем текущего пользователя
      localStorage.setItem('arceus_user', JSON.stringify(newUser));
      
      // Добавляем в общий список пользователей
      const usersData = localStorage.getItem('arceus_all_users');
      let allUsersArray = [];
      
      if (usersData) {
        try {
          allUsersArray = JSON.parse(usersData);
        } catch (error) {
          allUsersArray = [];
        }
      }
      
      allUsersArray.push(newUser);
      localStorage.setItem('arceus_all_users', JSON.stringify(allUsersArray));
      
      setUserData(newUser);
      setCurrentScreen('main');
      loadAllUsers();
      
    } catch (error) {
      console.error('Ошибка регистрации:', error);
      setError('Ошибка регистрации. Попробуйте снова.');
    } finally {
      setLoading(false);
    }
  };

  const startChat = (otherUser) => {
    const chatId = [userData.phone, otherUser.phone].sort().join('_');
    
    // Проверяем существующий чат
    const existingChat = chats.find(c => c.chatId === chatId);
    if (existingChat) {
      setSelectedChat(existingChat);
      loadChatMessages(chatId);
      return;
    }
    
    const newChat = {
      chatId: chatId,
      otherUser: otherUser,
      lastMessage: '',
      timestamp: new Date().toISOString()
    };

    // Сохраняем чат для текущего пользователя
    const myChats = [...chats, newChat];
    localStorage.setItem(`arceus_chats_${userData.phone}`, JSON.stringify(myChats));
    setChats(myChats);
    
    // Сохраняем чат для другого пользователя
    const otherChatsData = localStorage.getItem(`arceus_chats_${otherUser.phone}`);
    let otherChats = [];
    if (otherChatsData) {
      try {
        otherChats = JSON.parse(otherChatsData);
      } catch (error) {
        otherChats = [];
      }
    }
    
    otherChats.push({
      chatId: chatId,
      otherUser: userData,
      lastMessage: '',
      timestamp: new Date().toISOString()
    });
    
    localStorage.setItem(`arceus_chats_${otherUser.phone}`, JSON.stringify(otherChats));
    
    setSelectedChat(newChat);
    setMessages([]);
  };

  const handleSendMessage = () => {
    if (!message.trim() || !selectedChat) return;

    const newMessage = {
      id: Date.now(),
      sender: userData.phone,
      text: message.trim(),
      timestamp: new Date().toISOString()
    };

    // Сохраняем сообщение
    const chatMessages = [...messages, newMessage];
    localStorage.setItem(`arceus_messages_${selectedChat.chatId}`, JSON.stringify(chatMessages));
    setMessages(chatMessages);
    
    // Обновляем последнее сообщение в чате
    const updatedChats = chats.map(chat => 
      chat.chatId === selectedChat.chatId 
        ? { ...chat, lastMessage: message.trim(), timestamp: new Date().toISOString() }
        : chat
    );
    localStorage.setItem(`arceus_chats_${userData.phone}`, JSON.stringify(updatedChats));
    setChats(updatedChats);
    
    // Обновляем чат собеседника
    const otherChatsData = localStorage.getItem(`arceus_chats_${selectedChat.otherUser.phone}`);
    if (otherChatsData) {
      try {
        const otherChats = JSON.parse(otherChatsData);
        const updatedOtherChats = otherChats.map(chat =>
          chat.chatId === selectedChat.chatId
            ? { ...chat, lastMessage: message.trim(), timestamp: new Date().toISOString() }
            : chat
        );
        localStorage.setItem(`arceus_chats_${selectedChat.otherUser.phone}`, JSON.stringify(updatedOtherChats));
      } catch (error) {
        console.error('Ошибка обновления чата собеседника:', error);
      }
    }
    
    setMessage('');
  };

  const handleLogout = () => {
    localStorage.removeItem('arceus_user');
    setUserData(null);
    setCurrentScreen('phone');
    setPhoneNumber('');
    setNickname('');
    setChats([]);
    setMessages([]);
    setSelectedChat(null);
  };

  // Phone Registration Screen
  if (currentScreen === 'phone') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 flex items-center justify-center p-4">
        <div className="w-full max-w-md">
          <div className="text-center mb-8">
            <div className="inline-flex items-center justify-center w-20 h-20 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full mb-4 shadow-2xl">
              <MessageCircle size={40} className="text-white" />
            </div>
            <h1 className="text-4xl font-bold text-white mb-2">ArceusGramm</h1>
            <p className="text-purple-200">Реальный мессенджер</p>
          </div>

          <div className="bg-white/10 backdrop-blur-lg rounded-3xl p-8 shadow-2xl border border-white/20">
            <h2 className="text-2xl font-bold text-white mb-6">Регистрация</h2>
            
            <div className="mb-6 bg-blue-500/20 border border-blue-400/30 rounded-xl p-4">
              <div className="flex items-start gap-3">
                <AlertCircle className="text-blue-300 flex-shrink-0 mt-1" size={20} />
                <div className="text-blue-100 text-sm">
                  <strong>Важно:</strong> Этот мессенджер работает локально в вашем браузере. Чтобы общаться с другими людьми, они должны открыть ту же версию мессенджера на своём устройстве.
                </div>
              </div>
            </div>
            
            <div>
              <div className="mb-4">
                <label className="block text-purple-200 mb-2 text-sm">Выберите страну</label>
                <select
                  value={countryCode}
                  onChange={(e) => setCountryCode(e.target.value)}
                  className="w-full bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white focus:outline-none focus:ring-2 focus:ring-purple-500"
                >
                  <option value="+380" className="bg-purple-900">🇺🇦 Украина (+380)</option>
                  <option value="+7" className="bg-purple-900">🇷🇺 Россия (+7)</option>
                </select>
              </div>

              <div className="mb-6">
                <label className="block text-purple-200 mb-2 text-sm">Номер телефона</label>
                <div className="flex gap-2">
                  <div className="bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white font-semibold">
                    {countryCode}
                  </div>
                  <input
                    type="tel"
                    value={phoneNumber}
                    onChange={(e) => setPhoneNumber(e.target.value.replace(/\D/g, ''))}
                    placeholder="XX XXX XX XX"
                    className="flex-1 bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300 focus:outline-none focus:ring-2 focus:ring-purple-500"
                    maxLength="10"
                  />
                </div>
              </div>

              {error && <p className="text-red-300 mb-4 text-sm">{error}</p>}

              <button
                onClick={handlePhoneSubmit}
                disabled={loading}
                className="w-full bg-gradient-to-r from-purple-500 to-blue-500 text-white font-semibold py-3 rounded-xl hover:shadow-lg hover:scale-105 transition-all duration-200 disabled:opacity-50"
              >
                Продолжить
              </button>
            </div>
          </div>
        </div>
      </div>
    );
  }

  // Nickname Registration Screen
  if (currentScreen === 'nickname') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 flex items-center justify-center p-4">
        <div className="w-full max-w-md">
          <div className="text-center mb-8">
            <div className="inline-flex items-center justify-center w-20 h-20 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full mb-4 shadow-2xl">
              <User size={40} className="text-white" />
            </div>
            <h1 className="text-4xl font-bold text-white mb-2">Создайте никнейм</h1>
            <p className="text-purple-200">Это ваше имя в ArceusGramm</p>
          </div>

          <div className="bg-white/10 backdrop-blur-lg rounded-3xl p-8 shadow-2xl border border-white/20">
            <div>
              <div className="mb-6">
                <label className="block text-purple-200 mb-2 text-sm">Ваш никнейм</label>
                <input
                  type="text"
                  value={nickname}
                  onChange={(e) => setNickname(e.target.value)}
                  placeholder="Минимум 4 символа"
                  className="w-full bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300 focus:outline-none focus:ring-2 focus:ring-purple-500"
                  maxLength="20"
                />
                <p className="text-purple-300 text-xs mt-2">
                  Символов: {nickname.length}/20 (минимум 4)
                </p>
              </div>

              {error && <p className="text-red-300 mb-4 text-sm">{error}</p>}

              <button
                onClick={handleNicknameSubmit}
                disabled={loading}
                className="w-full bg-gradient-to-r from-purple-500 to-blue-500 text-white font-semibold py-3 rounded-xl hover:shadow-lg hover:scale-105 transition-all duration-200 disabled:opacity-50"
              >
                {loading ? 'Регистрация...' : 'Завершить регистрацию'}
              </button>
            </div>
          </div>
        </div>
      </div>
    );
  }

  // Profile Screen
  if (currentScreen === 'profile') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900">
        <div className="max-w-2xl mx-auto p-4">
          <div className="bg-white/10 backdrop-blur-lg rounded-3xl shadow-2xl border border-white/20 overflow-hidden">
            <div className="bg-gradient-to-r from-purple-600 to-blue-600 p-6">
              <button
                onClick={() => setCurrentScreen('main')}
                className="text-white mb-4 hover:bg-white/20 p-2 rounded-lg transition-all"
              >
                <ArrowLeft size={24} />
              </button>
              <div className="flex items-center gap-4">
                <div className="w-24 h-24 bg-white/20 rounded-full flex items-center justify-center border-4 border-white/30">
                  <User size={48} className="text-white" />
                </div>
                <div>
                  <h2 className="text-3xl font-bold text-white">{userData.nickname}</h2>
                  <p className="text-purple-200">Онлайн</p>
                </div>
              </div>
            </div>

            <div className="p-6 space-y-4">
              <div className="bg-white/5 rounded-2xl p-6 border border-white/10">
                <div className="flex items-center gap-3 mb-4">
                  <Phone className="text-purple-400" size={24} />
                  <div>
                    <p className="text-purple-300 text-sm">Номер телефона</p>
                    <p className="text-white text-lg font-semibold">{userData.phone}</p>
                  </div>
                </div>
              </div>

              <div className="bg-white/5 rounded-2xl p-6 border border-white/10">
                <div className="flex items-center gap-3 mb-4">
                  <User className="text-blue-400" size={24} />
                  <div>
                    <p className="text-purple-300 text-sm">Никнейм</p>
                    <p className="text-white text-lg font-semibold">@{userData.nickname}</p>
                  </div>
                </div>
              </div>

              <div className="bg-white/5 rounded-2xl p-6 border border-white/10">
                <div className="flex items-center gap-3 mb-4">
                  <MessageCircle className="text-green-400" size={24} />
                  <div>
                    <p className="text-purple-300 text-sm">Страна</p>
                    <p className="text-white text-lg font-semibold">{userData.country}</p>
                  </div>
                </div>
              </div>

              <button
                onClick={handleLogout}
                className="w-full bg-red-500/20 hover:bg-red-500/30 border border-red-500/50 text-red-200 font-semibold py-3 rounded-xl transition-all"
              >
                Выйти из аккаунта
              </button>
            </div>
          </div>
        </div>
      </div>
    );
  }

  // Users List Screen
  if (currentScreen === 'users') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900">
        <div className="max-w-2xl mx-auto p-4">
          <div className="bg-white/10 backdrop-blur-lg rounded-3xl shadow-2xl border border-white/20 overflow-hidden">
            <div className="bg-gradient-to-r from-purple-600 to-blue-600 p-6">
              <button
                onClick={() => setCurrentScreen('main')}
                className="text-white mb-4 hover:bg-white/20 p-2 rounded-lg transition-all"
              >
                <ArrowLeft size={24} />
              </button>
              <h2 className="text-3xl font-bold text-white">Все пользователи</h2>
              <p className="text-purple-200">{allUsers.length} пользователей</p>
            </div>

            <div className="p-4 space-y-2 max-h-[600px] overflow-y-auto">
              {allUsers.length === 0 ? (
                <div className="text-center py-12">
                  <div className="inline-flex items-center justify-center w-20 h-20 bg-white/10 rounded-full mb-4">
                    <Users size={40} className="text-purple-400" />
                  </div>
                  <p className="text-purple-300 mb-2">Пока нет других пользователей</p>
                  <p className="text-purple-400 text-sm">Поделитесь ссылкой с друзьями!</p>
                </div>
              ) : (
                allUsers.map((user, index) => (
                  <div
                    key={index}
                    onClick={() => {
                      startChat(user);
                      setCurrentScreen('main');
                    }}
                    className="bg-white/5 hover:bg-white/10 rounded-2xl p-4 border border-white/10 cursor-pointer transition-all"
                  >
                    <div className="flex items-center gap-3">
                      <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                        <User className="text-white" size={24} />
                      </div>
                      <div>
                        <h3 className="text-white font-semibold">{user.nickname}</h3>
                        <p className="text-purple-300 text-sm">{user.country}</p>
                      </div>
                    </div>
                  </div>
                ))
              )}
            </div>
          </div>
        </div>
      </div>
    );
  }

  // Main Messenger Screen
  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900">
      <div className="max-w-6xl mx-auto flex h-screen">
        {/* Sidebar */}
        <div className="w-96 bg-white/10 backdrop-blur-lg border-r border-white/20">
          <div className="p-4 bg-gradient-to-r from-purple-600 to-blue-600">
            <div className="flex items-center justify-between mb-4">
              <h1 className="text-2xl font-bold text-white">ArceusGramm</h1>
              <div className="flex gap-2">
                <button
                  onClick={() => setCurrentScreen('users')}
                  className="p-2 hover:bg-white/20 rounded-full transition-all"
                  title="Все пользователи"
                >
                  <Users className="text-white" size={24} />
                </button>
                <button
                  onClick={() => setCurrentScreen('profile')}
                  className="p-2 hover:bg-white/20 rounded-full transition-all"
                >
                  <User className="text-white" size={24} />
                </button>
              </div>
            </div>
            <div className="relative">
              <Search className="absolute left-3 top-3 text-purple-300" size={20} />
              <input
                type="text"
                placeholder="Поиск..."
                className="w-full bg-white/20 border border-white/30 rounded-xl pl-10 pr-4 py-2 text-white placeholder-purple-300 focus:outline-none focus:ring-2 focus:ring-purple-500"
              />
            </div>
          </div>

          <div className="overflow-y-auto h-[calc(100vh-140px)]">
            {chats.length === 0 ? (
              <div className="text-center py-8">
                <p className="text-purple-300 mb-4">Пока нет чатов</p>
                <button
                  onClick={() => setCurrentScreen('users')}
                  className="bg-purple-500 hover:bg-purple-600 text-white px-4 py-2 rounded-lg transition-all"
                >
                  Найти пользователей
                </button>
              </div>
            ) : (
              chats.map((chat, index) => (
                <div
                  key={index}
                  onClick={() => {
                    setSelectedChat(chat);
                    loadChatMessages(chat.chatId);
                  }}
                  className={`p-4 border-b border-white/10 cursor-pointer transition-all ${
                    selectedChat?.chatId === chat.chatId ? 'bg-white/20' : 'hover:bg-white/10'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                      <User className="text-white" size={24} />
                    </div>
                    <div className="flex-1">
                      <h3 className="text-white font-semibold">{chat.otherUser.nickname}</h3>
                      <p className="text-purple-300 text-sm truncate">{chat.lastMessage || 'Новый чат'}</p>
                    </div>
                  </div>
                </div>
              ))
            )}
          </div>
        </div>

        {/* Main Chat Area */}
        <div className="flex-1 flex flex-col">
          {!selectedChat ? (
            <div className="flex-1 flex items-center justify-center">
              <div className="text-center">
                <div className="inline-flex items-center justify-center w-32 h-32 bg-white/10 rounded-full mb-4">
                  <MessageCircle size={64} className="text-purple-400" />
                </div>
                <h2 className="text-2xl font-bold text-white mb-2">Добро пожаловать, {userData.nickname}!</h2>
                <p className="text-purple-300 mb-4">Выберите чат или начните новый</p>
                <button
                  onClick={() => setCurrentScreen('users')}
                  className="bg-gradient-to-r from-purple-500 to-blue-500 text-white px-6 py-3 rounded-xl hover:shadow-lg hover:scale-105 transition-all"
                >
                  Найти пользователей
                </button>
              </div>
            </div>
          ) : (
            <>
              {/* Chat Header */}
              <div className="bg-white/10 backdrop-blur-lg border-b border-white/20 p-4">
                <div className="flex items-center justify-between">
                  <div className="flex items-center gap-3">
                    <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                      <User className="text-white" size={24} />
                    </div>
                    <div>
                      <h3 className="text-white font-semibold">{selectedChat.otherUser.nickname}</h3>
                      <p className="text-purple-300 text-sm">Онлайн</p>
                    </div>
                  </div>
                  <button className="p-2 hover:bg-white/20 rounded-full transition-all">
                    <MoreVertical className="text-white" size={24} />
                  </button>
                </div>
              </div>

              {/* Messages Area */}
              <div className="flex-1 overflow-y-auto p-4 space-y-3">
                {messages.length === 0 ? (
                  <div className="text-center text-purple-300 py-8">
                    Начните общение с {selectedChat.otherUser.nickname}
                  </div>
                ) : (
                  messages.map((msg) => (
                    <div
                      key={msg.id}
                      className={`flex ${msg.sender === userData.phone ? 'justify-end' : 'justify-start'}`}
                    >
                      <div
                        className={`max-w-md px-4 py-2 rounded-2xl ${
                          msg.sender === userData.phone
                            ? 'bg-gradient-to-r from-purple-500 to-blue-500 text-white'
                            : 'bg-white/20 text-white'
                        }`}
                      >
                        <p>{msg.text}</p>
                        <p className="text-xs opacity-70 mt-1">
                          {new Date(msg.timestamp).toLocaleTimeString('ru-RU', {
                            hour: '2-digit',
                            minute: '2-digit'
                          })}
                        </p>
                      </div>
                    </div>
                  ))
                )}
              </div>

              {/* Message Input */}
              <div className="bg-white/10 backdrop-blur-lg border-t border-white/20 p-4">
                <div className="flex gap-2">
                  <input
                    type="text"
                    value={message}
                    onChange={(e) => setMessage(e.target.value)}
                    onKeyPress={(e) => e.key === 'Enter' && handleSendMessage()}
                    placeholder="Напишите сообщение..."
                    className="flex-1 bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300 focus:outline-none focus:ring-2 focus:ring-purple-500"
                  />
                  <button
                    onClick={handleSendMessage}
                    className="bg-gradient-to-r from-purple-500 to-blue-500 text-white p-3 rounded-xl hover:shadow-lg hover:scale-105 transition-all"
                  >
                    <Send size={24} />
                  </button>
                </div>
              </div>
            </>
          )}
        </div>
      </div>
    </div>
  );
};

export default ArceusMessenger;
