# Adventure Battle Game
# A simple text-based RPG where you explore rooms, fight monsters, find treasures, level up, and try to survive as long as possible.

import random
import sys

class Character:
    def __init__(self, name, hp, attack, defense):
        self.name = name
        self.max_hp = hp
        self.hp = hp
        self.attack = attack
        self.defense = defense

    def is_alive(self):
        return self.hp > 0

    def take_damage(self, dmg):
        damage = max(0, dmg - self.defense)
        self.hp = max(0, self.hp - damage)
        return damage

    def deal_damage(self, other):
        dmg = random.randint(self.attack - 2, self.attack + 2)
        return other.take_damage(dmg)

class Player(Character):
    def __init__(self):
        super().__init__("Hero", hp=100, attack=10, defense=5)
        self.xp = 0
        self.gold = 0
        self.level = 1

    def level_up(self):
        needed = self.level * 50
        if self.xp >= needed:
            self.level += 1
            self.attack += 2
            self.defense += 1
            self.max_hp += 10
            self.hp = self.max_hp
            self.xp -= needed
            print(f"\n*** Level Up! You are now level {self.level}! ***")

    def heal(self, amount):
        self.hp = min(self.max_hp, self.hp + amount)

class Monster(Character):
    def __init__(self, level):
        hp = random.randint(20, 20 + level * 10)
        attack = random.randint(5, 5 + level * 2)
        defense = random.randint(1, 1 + level)
        super().__init__("Monster", hp, attack, defense)


def fight(player, monster):
    print(f"A wild monster appears! HP:{monster.hp}, ATK:{monster.attack}, DEF:{monster.defense}")
    while player.is_alive() and monster.is_alive():
        cmd = input("[1] Attack  [2] Flee > ")
        if cmd == "1":
            dmg = player.deal_damage(monster)
            print(f"You attack the monster for {dmg} damage. (Monster HP: {monster.hp}/{monster.max_hp})")
        elif cmd == "2":
            if random.random() < 0.5:
                print("You successfully fled!")
                return False
            else:
                print("Failed to flee!")
        else:
            print("Invalid choice.")
            continue
        if monster.is_alive():
            dmg = monster.deal_damage(player)
            print(f"Monster strikes you for {dmg} damage. (Your HP: {player.hp}/{player.max_hp})")
    return player.is_alive()


def find_treasure(player):
    print("You found a treasure chest...")
    choice = random.choice(["gold", "potion"])
    if choice == "gold":
        amount = random.randint(10, 30)
        player.gold += amount
        print(f"You obtained {amount} gold! (Total Gold: {player.gold})")
    else:
        amount = random.randint(20, 50)
        player.heal(amount)
        print(f"You found a healing potion and recovered {amount} HP! (HP: {player.hp}/{player.max_hp})")


def main():
    print("=== Adventure Battle Game ===")
    player = Player()
    room_count = 0
    while player.is_alive():
        cmd = input("\nPress Enter to explore next room (or 'q' to quit) > ")
        if cmd.lower() == 'q':
            print("You chose to end your adventure. Goodbye!")
            break
        room_count += 1
        print(f"\n-- Room {room_count} --")
        event = random.random()
        if event < 0.7:
            monster = Monster(player.level)
            survived = fight(player, monster)
            if not survived:
                print("You have been defeated. Game Over.")
                break
            xp_gain = random.randint(15, 30)
            gold_gain = random.randint(5, 20)
            player.xp += xp_gain
            player.gold += gold_gain
            print(f"You defeated the monster! +{xp_gain} XP, +{gold_gain} Gold.")
            player.level_up()
        else:
            find_treasure(player)
        print(f"Status - HP:{player.hp}/{player.max_hp} | LV:{player.level} XP:{player.xp} | Gold:{player.gold}")
    print("=== Thank you for playing! ===")

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print("\nAdventure ended.")
        sys.exit()
