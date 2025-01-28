# Baloot-Game
// داخل حدث joinRoom عند اكتمال اللاعبين:
const players = room.players; // القائمة تحتوي على socket.id للاعبين
const hands = {};
players.forEach((playerId, index) => {
  hands[playerId] = deck.slice(index * 8, (index + 1) * 8);
});
room.hands = hands;
useEffect(() => {
  socket.on("gameStarted", (hands) => {
    const playerHand = hands[socket.id]; // استخدم socket.id بدلًا من player1
    setHand(playerHand);
  });
}, []);
socket.on('playCard', ({ roomId, card }) => {
  const room = rooms.get(roomId);
  room.table = room.table || [];
  room.table.push({ player: socket.id, card });
  // إرسال التحديث لجميع اللاعبين
  io.to(roomId).emit('updateTable', room.table);
});
const playCard = (card) => {
  socket.emit('playCard', { roomId, card });
};

// في مكون البطاقة:
<div onClick={() => playCard(card)}> ... </div>
import React from 'react';
import Card from './Card';

const PlayerHand = ({ hand, onPlayCard }) => {
  return (
    <div className="flex flex-wrap gap-4 justify-center">
      {hand.map((card, index) => (
        <Card 
          key={index} 
          card={card} 
          onClick={() => onPlayCard(card)}
        />
      ))}
    </div>
  );
};

export default PlayerHand;
useEffect(() => {
  socket.on('updateTable', (table) => {
    setTable(table);
  });
  return () => socket.off('updateTable'); // تنظيف الاشتراك
}, []);
