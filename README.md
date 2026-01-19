import React, { useState, useEffect } from 'react';
import { MessageCircle, User, Phone, Send, Users, ArrowLeft } from 'lucide-react';

export default function ArceusMessenger() {
  const [screen, setScreen] = useState('phone');
  const [phone, setPhone] = useState('');
  const [code, setCode] = useState('+380');
  const [nick, setNick] = useState('');
  const [error, setError] = useState('');
  const [user, setUser] = useState(null);
  const [users, setUsers] = useState([]);
  const [chats, setChats] = useState([]);
  const [chat, setChat] = useState(null);
  const [msgs, setMsgs] = useState([]);
  const [msg, setMsg] = useState('');

  useEffect(() => {
    const saved = localStorage.getItem('ag_user');
    if (saved) {
      setUser(JSON.parse(saved));
      setScreen('main');
    }
  }, []);

  useEffect(() => {
    if (user && screen === 'main') {
      loadUsers();
      loadChats();
      const int = setInterval(() => {
        loadUsers();
        loadChats();
        if (chat) loadMsgs(chat.id);
      }, 3000);
      return () => clearInterval(int);
    }
  }, [user, screen, chat]);

  const loadUsers = () => {
    const data = localStorage.getItem('ag_users');
    if (data) {
      const all = JSON.parse(data);
      setUsers(all.filter(u => u.phone !== user?.phone));
    }
  };

  const loadChats = () => {
    if (!user) return;
    const data = localStorage.getItem(`ag_chats_${user.phone}`);
    if (data) setChats(JSON.parse(data));
  };

  const loadMsgs = (id) => {
    const data = localStorage.getItem(`ag_msgs_${id}`);
    if (data) setMsgs(JSON.parse(data));
  };

  const register = () => {
    if (nick.length < 4) {
      setError('Минимум 4 символа');
      return;
    }
    const fullPhone = code + phone;
    const newUser = {
      phone: fullPhone,
      nick,
      country: code === '+380' ? 'Украина' : 'Россия',
      time: new Date().toISOString()
    };
    
    localStorage.setItem('ag_user', JSON.stringify(newUser));
    
    const all = JSON.parse(localStorage.getItem('ag_users') || '[]');
    all.push(newUser);
    localStorage.setItem('ag_users', JSON.stringify(all));
    
    setUser(newUser);
    setScreen('main');
  };

  const startChat = (other) => {
    const id = [user.phone, other.phone].sort().join('_');
    const existing = chats.find(c => c.id === id);
    if (existing) {
      setChat(existing);
      loadMsgs(id);
      return;
    }
    
    const newChat = { id, other, last: '', time: new Date().toISOString() };
    const myChats = [...chats, newChat];
    localStorage.setItem(`ag_chats_${user.phone}`, JSON.stringify(myChats));
    setChats(myChats);
    
    const otherChats = JSON.parse(localStorage.getItem(`ag_chats_${other.phone}`) || '[]');
    otherChats.push({ id, other: user, last: '', time: new Date().toISOString() });
    localStorage.setItem(`ag_chats_${other.phone}`, JSON.stringify(otherChats));
    
    setChat(newChat);
    setMsgs([]);
  };

  const send = () => {
    if (!msg.trim() || !chat) return;
    
    const newMsg = {
      id: Date.now(),
      from: user.phone,
      text: msg.trim(),
      time: new Date().toISOString()
    };
    
    const all = [...msgs, newMsg];
    localStorage.setItem(`ag_msgs_${chat.id}`, JSON.stringify(all));
    setMsgs(all);
    
    const updated = chats.map(c => 
      c.id === chat.id ? { ...c, last: msg.trim(), time: new Date().toISOString() } : c
    );
    localStorage.setItem(`ag_chats_${user.phone}`, JSON.stringify(updated));
    setChats(updated);
    
    setMsg('');
  };

  if (screen === 'phone') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 flex items-center justify-center p-4">
        <div className="w-full max-w-md">
          <div className="text-center mb-8">
            <div className="inline-flex items-center justify-center w-20 h-20 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full mb-4">
              <MessageCircle size={40} className="text-white" />
            </div>
            <h1 className="text-4xl font-bold text-white mb-2">ArceusGramm</h1>
            <p className="text-purple-200">Ваш мессенджер</p>
          </div>
          <div className="bg-white/10 backdrop-blur-lg rounded-3xl p-8 border border-white/20">
            <h2 className="text-2xl font-bold text-white mb-6">Регистрация</h2>
            <select value={code} onChange={(e) => setCode(e.target.value)} className="w-full bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white mb-4">
              <option value="+380" className="bg-purple-900">🇺🇦 Украина (+380)</option>
              <option value="+7" className="bg-purple-900">🇷🇺 Россия (+7)</option>
            </select>
            <div className="flex gap-2 mb-4">
              <div className="bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white font-semibold">{code}</div>
              <input type="tel" value={phone} onChange={(e) => setPhone(e.target.value.replace(/\D/g, ''))} placeholder="XX XXX XX XX" className="flex-1 bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300" maxLength="10" />
            </div>
            {phone.length >= 9 && (
              <>
                <input type="text" value={nick} onChange={(e) => setNick(e.target.value)} placeholder="Ваш никнейм (мин 4 символа)" className="w-full bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300 mb-4" maxLength="20" />
                {error && <p className="text-red-300 mb-4 text-sm">{error}</p>}
                <button onClick={register} className="w-full bg-gradient-to-r from-purple-500 to-blue-500 text-white font-semibold py-3 rounded-xl hover:shadow-lg">Зарегистрироваться</button>
              </>
            )}
          </div>
        </div>
      </div>
    );
  }

  if (screen === 'users') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 p-4">
        <div className="max-w-2xl mx-auto bg-white/10 backdrop-blur-lg rounded-3xl border border-white/20 overflow-hidden">
          <div className="bg-gradient-to-r from-purple-600 to-blue-600 p-6">
            <button onClick={() => setScreen('main')} className="text-white mb-4 hover:bg-white/20 p-2 rounded-lg">
              <ArrowLeft size={24} />
            </button>
            <h2 className="text-3xl font-bold text-white">Пользователи ({users.length})</h2>
          </div>
          <div className="p-4 space-y-2 max-h-[600px] overflow-y-auto">
            {users.map((u, i) => (
              <div key={i} onClick={() => { startChat(u); setScreen('main'); }} className="bg-white/5 hover:bg-white/10 rounded-2xl p-4 border border-white/10 cursor-pointer">
                <div className="flex items-center gap-3">
                  <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                    <User className="text-white" size={24} />
                  </div>
                  <div>
                    <h3 className="text-white font-semibold">{u.nick}</h3>
                    <p className="text-purple-300 text-sm">{u.country}</p>
                  </div>
                </div>
              </div>
            ))}
          </div>
        </div>
      </div>
    );
  }

  if (screen === 'profile') {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 p-4">
        <div className="max-w-2xl mx-auto bg-white/10 backdrop-blur-lg rounded-3xl border border-white/20 overflow-hidden">
          <div className="bg-gradient-to-r from-purple-600 to-blue-600 p-6">
            <button onClick={() => setScreen('main')} className="text-white mb-4 hover:bg-white/20 p-2 rounded-lg">
              <ArrowLeft size={24} />
            </button>
            <div className="flex items-center gap-4">
              <div className="w-24 h-24 bg-white/20 rounded-full flex items-center justify-center border-4 border-white/30">
                <User size={48} className="text-white" />
              </div>
              <div>
                <h2 className="text-3xl font-bold text-white">{user.nick}</h2>
                <p className="text-purple-200">Онлайн</p>
              </div>
            </div>
          </div>
          <div className="p-6 space-y-4">
            <div className="bg-white/5 rounded-2xl p-6 border border-white/10">
              <Phone className="text-purple-400 mb-2" size={24} />
              <p className="text-purple-300 text-sm">Телефон</p>
              <p className="text-white text-lg font-semibold">{user.phone}</p>
            </div>
            <div className="bg-white/5 rounded-2xl p-6 border border-white/10">
              <User className="text-blue-400 mb-2" size={24} />
              <p className="text-purple-300 text-sm">Никнейм</p>
              <p className="text-white text-lg font-semibold">@{user.nick}</p>
            </div>
            <button onClick={() => { localStorage.clear(); window.location.reload(); }} className="w-full bg-red-500/20 border border-red-500/50 text-red-200 font-semibold py-3 rounded-xl">Выйти</button>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900">
      <div className="max-w-6xl mx-auto flex h-screen">
        <div className="w-96 bg-white/10 backdrop-blur-lg border-r border-white/20">
          <div className="p-4 bg-gradient-to-r from-purple-600 to-blue-600">
            <div className="flex items-center justify-between mb-4">
              <h1 className="text-2xl font-bold text-white">ArceusGramm</h1>
              <div className="flex gap-2">
                <button onClick={() => setScreen('users')} className="p-2 hover:bg-white/20 rounded-full">
                  <Users className="text-white" size={24} />
                </button>
                <button onClick={() => setScreen('profile')} className="p-2 hover:bg-white/20 rounded-full">
                  <User className="text-white" size={24} />
                </button>
              </div>
            </div>
          </div>
          <div className="overflow-y-auto h-[calc(100vh-100px)]">
            {chats.length === 0 ? (
              <div className="text-center py-8">
                <p className="text-purple-300 mb-4">Нет чатов</p>
                <button onClick={() => setScreen('users')} className="bg-purple-500 text-white px-4 py-2 rounded-lg">Найти людей</button>
              </div>
            ) : (
              chats.map((c, i) => (
                <div key={i} onClick={() => { setChat(c); loadMsgs(c.id); }} className={`p-4 border-b border-white/10 cursor-pointer ${chat?.id === c.id ? 'bg-white/20' : 'hover:bg-white/10'}`}>
                  <div className="flex items-center gap-3">
                    <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                      <User className="text-white" size={24} />
                    </div>
                    <div className="flex-1">
                      <h3 className="text-white font-semibold">{c.other.nick}</h3>
                      <p className="text-purple-300 text-sm truncate">{c.last || 'Новый чат'}</p>
                    </div>
                  </div>
                </div>
              ))
            )}
          </div>
        </div>
        <div className="flex-1 flex flex-col">
          {!chat ? (
            <div className="flex-1 flex items-center justify-center">
              <div className="text-center">
                <MessageCircle size={64} className="text-purple-400 mx-auto mb-4" />
                <h2 className="text-2xl font-bold text-white mb-2">Привет, {user.nick}!</h2>
                <button onClick={() => setScreen('users')} className="bg-gradient-to-r from-purple-500 to-blue-500 text-white px-6 py-3 rounded-xl">Найти людей</button>
              </div>
            </div>
          ) : (
            <>
              <div className="bg-white/10 backdrop-blur-lg border-b border-white/20 p-4">
                <div className="flex items-center gap-3">
                  <div className="w-12 h-12 bg-gradient-to-br from-purple-500 to-blue-500 rounded-full flex items-center justify-center">
                    <User className="text-white" size={24} />
                  </div>
                  <h3 className="text-white font-semibold">{chat.other.nick}</h3>
                </div>
              </div>
              <div className="flex-1 overflow-y-auto p-4 space-y-3">
                {msgs.map((m) => (
                  <div key={m.id} className={`flex ${m.from === user.phone ? 'justify-end' : 'justify-start'}`}>
                    <div className={`max-w-md px-4 py-2 rounded-2xl ${m.from === user.phone ? 'bg-gradient-to-r from-purple-500 to-blue-500 text-white' : 'bg-white/20 text-white'}`}>
                      <p>{m.text}</p>
                      <p className="text-xs opacity-70 mt-1">{new Date(m.time).toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit' })}</p>
                    </div>
                  </div>
                ))}
              </div>
              <div className="bg-white/10 backdrop-blur-lg border-t border-white/20 p-4">
                <div className="flex gap-2">
                  <input type="text" value={msg} onChange={(e) => setMsg(e.target.value)} onKeyPress={(e) => e.key === 'Enter' && send()} placeholder="Сообщение..." className="flex-1 bg-white/20 border border-white/30 rounded-xl px-4 py-3 text-white placeholder-purple-300" />
                  <button onClick={send} className="bg-gradient-to-r from-purple-500 to-blue-500 text-white p-3 rounded-xl">
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
}
