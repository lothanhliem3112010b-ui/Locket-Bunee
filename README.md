import React, { useState, useRef, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { Camera, Image as ImageIcon, RefreshCcw, MessageCircle, Users, ChevronDown, Zap } from 'lucide-react';

// --- Component Emoji Bay ---
const FlyingEmoji = ({ id, emoji, onComplete }) => {
  return (
    <motion.div
      initial={{ y: 0, x: 0, opacity: 1, scale: 1 }}
      animate={{ 
        y: -500, 
        x: (Math.random() - 0.5) * 200, // Bay lệch trái/phải ngẫu nhiên
        opacity: 0, 
        scale: 1.5,
        rotate: Math.random() * 45 
      }}
      transition={{ duration: 2, ease: "easeOut" }}
      onAnimationComplete={() => onComplete(id)}
      className="absolute bottom-20 text-4xl pointer-events-none z-50"
    >
      {emoji}
    </motion.div>
  );
};

const LocketBu = () => {
  const [emojis, setEmojis] = useState([]);
  const [stream, setStream] = useState(null);
  const videoRef = useRef(null);

  // Hàm tạo emoji khi click
  const addEmoji = (e) => {
    const list = ['❤️', '🔥', '😂', '😍', '👍', '✨'];
    const newEmoji = {
      id: Date.now(),
      char: list[Math.floor(Math.random() * list.length)]
    };
    setEmojis((prev) => [...prev, newEmoji]);
  };

  const removeEmoji = (id) => {
    setEmojis((prev) => prev.filter(e => e.id !== id));
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-[#3a0210] via-[#2a010a] to-[#000000] text-white flex flex-col items-center overflow-hidden font-sans">
      
      {/* 1. TOP BAR */}
      <header className="w-full max-w-md p-6 flex justify-between items-center z-10">
        <div className="w-10 h-10 rounded-full overflow-hidden border-2 border-zinc-700 shadow-lg">
          <img src="https://i.pravatar.cc/100" alt="avatar" />
        </div>
        
        <button className="bg-white/10 backdrop-blur-md px-4 py-2 rounded-full flex items-center gap-2 text-sm font-medium border border-white/5">
          <Users size={16} />
          <span>63 người bạn</span>
        </button>

        <button className="p-2 bg-white/10 rounded-full backdrop-blur-md">
          <MessageCircle size={22} fill="white" className="opacity-80" />
        </button>
      </header>

      {/* 2. CAMERA PREVIEW (HÌNH VUÔNG BO TRÒN CỰC ĐẠI) */}
      <main className="flex-1 w-full max-w-md flex flex-col items-center justify-center px-6 relative">
        <h2 className="absolute top-4 text-zinc-500 font-bold tracking-[0.2em] text-xs uppercase">Locket Bu</h2>
        
        <div className="relative w-full aspect-square bg-black rounded-[60px] overflow-hidden shadow-2xl border border-white/5">
          {/* Flash & Zoom icon */}
          <Zap className="absolute top-6 left-6 text-white/70" size={20} />
          <span className="absolute top-6 right-6 text-white/70 font-bold text-xs">1x</span>
          
          {/* Video Stream Placeholder */}
          <div className="w-full h-full flex items-center justify-center bg-zinc-900/50">
             <p className="text-zinc-600 text-sm italic">Camera đang sẵn sàng...</p>
          </div>
        </div>

        {/* Emoji Container - Nơi emoji bay lên */}
        <AnimatePresence>
          {emojis.map(e => (
            <FlyingEmoji key={e.id} id={e.id} emoji={e.char} onComplete={removeEmoji} />
          ))}
        </AnimatePresence>
      </main>

      {/* 3. BOTTOM CONTROLS */}
      <footer className="w-full max-w-md p-8 pb-12 flex flex-col items-center gap-8">
        
        <div className="flex w-full justify-between items-center px-4">
          <button className="p-3 bg-white/10 rounded-2xl">
            <ImageIcon size={28} />
          </button>

          {/* Shutter Button - Nút bấm và cũng là nút thả emoji */}
          <div className="relative group">
            <button 
              onClick={addEmoji}
              className="w-20 h-20 bg-white rounded-full p-1 transition-transform active:scale-90"
            >
              <div className="w-full h-full rounded-full border-[4px] border-transparent bg-clip-border bg-gradient-to-tr from-pink-500 to-orange-400 p-1">
                 <div className="w-full h-full bg-white rounded-full" />
              </div>
            </button>
          </div>

          <button className="p-3 bg-white/10 rounded-full">
            <RefreshCcw size={28} />
          </button>
        </div>

        {/* 4. HISTORY SECTION */}
        <div className="flex flex-col items-center gap-2 cursor-pointer opacity-80 hover:opacity-100 transition">
          <div className="flex items-center gap-2">
            <span className="bg-[#ff2d55] text-white text-[10px] font-black px-1.5 py-0.5 rounded-md">16</span>
            <span className="font-bold text-sm tracking-wide">Lịch sử</span>
          </div>
          <ChevronDown size={20} className="animate-bounce" />
        </div>
      </footer>

      {/* iPhone Indicator bar */}
      <div className="w-32 h-1 bg-white/20 rounded-full mb-2" />
    </div>
  );
};

export default LocketBu;
