// Test 1

using System;
using System.Collections.Generic;

public class Game
{
	public static void Main(string[] args)
	{
		Console.WriteLine("A peaceful village has been invaded by monstrous creature known as Murlocs.\nHelp the villagers to reclaim the village and defeat the enemies and their boss Murlocs.");
		Console.WriteLine("\nPlayer enters the battleground without any weapons but with courage!!!!");
		Console.WriteLine("\nPlayer must embark on a six-level journey, collecting four essential items along the way: \n\n1. Sword (Reward for completing Level 2) \n2. Shield (Reward for completing Level 3) \n3. Armour (Reward for completing Level 4) \n4. Bow (Reward for completing Level 5)");

		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();

		Console.WriteLine("\n");

		Player P1 = new Player();
		P1.Health = 100;

		Levels levels = new Levels();
		levels.Level1(P1);

		int SwordPower = levels.Level2(P1);

		int ShieldPower = levels.Level3(P1, SwordPower);

		int ArmourPower = levels.Level4(P1, ShieldPower);

		int BowPower = levels.Level5(P1, ArmourPower);

		levels.Level6(P1, BowPower);

	}


}


//interface for the property health which will be accessible by both the player and the enemy
public interface IHealth
{
	int Health
	{
		get;
		set;
	}
}

//interface for rewards which will be awarded to the player whenever a level is cleared
public interface IRewards
{
	string Rewards(string reward);
}

public class Character : IHealth
{
	private int health;
	public int Health
	{
		get
		{
			return health;
		}
		set
		{
			health = value;
		}
	}

	public void Attack(Player player)
	{
		Random random = new Random();
		int damage = random.Next(5, 35);
		Console.WriteLine("\nEnemy attacks! Player takes " + damage + " damage.");
		player.TakeDamage(damage);
		Console.WriteLine("Player Health: " + player.Health);
	}


	public void TakeDamage(int damage)
	{
		Health -= damage;
	}
}

public class Player : Character
{
	protected int Melee_Damage = 20;
	protected int Ranged_Damage = 40;
	protected int Defense = 10;
	protected List<string> Special_Abilities = new List<string>();


	//Allows the player to choose between actions Attack or Heal
	public void ChooseAction(Character enemy, int bonus)
	{
		Console.WriteLine("\nChoose Action:");
		Console.WriteLine("[A/a] Attack ");
		Console.WriteLine("[D/d] Defend ");
		Console.WriteLine("[H/h] Heal ");

		char ch = char.Parse(Console.ReadLine());

		if(ch == 'A' || ch == 'a')
		{
			Console.WriteLine("\nPlayer Attacking.....");
			Console.WriteLine("\nChoose Type of Attack: ");
			Console.WriteLine("[M/m] Melee Damage ");
			Console.WriteLine("[R/r] Ranged Damage ");
			char type = char.Parse(Console.ReadLine());

			if(type == 'M' || type == 'm')
			{
				if(Special_Abilities.Contains("Critical Hits"))
				{
					Melee_Damage = Melee_Damage * 2 + bonus;
					enemy.TakeDamage(Melee_Damage);
					Console.WriteLine("\nCritical Hits!!! Enemy Health after dealing with massive melee damage: " + enemy.Health);
				}
				else
				{
					Melee_Damage += bonus;
					enemy.TakeDamage(Melee_Damage);
					Console.WriteLine("\nEnemy Health after melee damage: " + enemy.Health);
				}
			}
			else if(type == 'R' || type == 'r')
			{
				if (Special_Abilities.Contains("Ranged Attack"))
				{
					Console.WriteLine("Ranged Attack Activated! Prevents damage from the next enemy attack!");
				}

				Ranged_Damage += bonus;
				enemy.TakeDamage(Ranged_Damage);
				Console.WriteLine("\nEnemy Health after ranged damage: " + enemy.Health);
			}
			else
			{
				Console.WriteLine("\nInvalid Choice! Try Again");
			}
		}

		else if(ch == 'H' || ch == 'h')
		{
			Console.WriteLine("\nPlayer Healing.....");
			if (Special_Abilities.Contains("Life Steal"))
			{
				Health += 50;
				Console.WriteLine("Life Steal Activated! Additional healing applied.");
			}
			else
			{
				Health += 30;
			}
			Console.WriteLine("Renewed Player Health: " + Health);

		}

		else if (ch == 'D' || ch == 'd')
		{
			Console.WriteLine("\nPlayer Defending...");

			Random random = new Random();
			int DamageByEnemy = random.Next(5, 35);

			if (Special_Abilities.Contains("Blocker"))
			{
				Console.WriteLine("Blocker Activated! Nullifies incoming damage.");
			}

			else
			{
				int reducedDamage = DamageByEnemy - (Defense + bonus);
				Health -= reducedDamage;

				int amountReduced = (Defense + bonus);
				Console.WriteLine("\nEnemy attacks! (Actual Damage: " + DamageByEnemy + " ) Damage reduced by " + amountReduced +" . Player takes " + reducedDamage + " damage.");
				Console.WriteLine("Player Health after defending: " + Health);
			}
		}

		else
		{
			Console.WriteLine("\nInvalid Choice! Try Again");
			ChooseAction(enemy,0);
		}

	}


	//Prints information about the death of the enemy/enemies.
	public void EnemyDied()
	{
		Console.WriteLine("\nPlayer Attacked");
		Console.WriteLine("Enemy Defeated");
		Console.WriteLine("Player won the fight");
	}

	public bool SpecialAbilityGeneration(string special_ability)
	{
		double activationProbability = 0.1;

		Random random = new Random();

		double randomValue = random.NextDouble();

		if(randomValue == activationProbability)
		{
			Special_Abilities.Add(special_ability);
			Console.WriteLine("\nSpecial Ability Unlocked: " + special_ability);
			return true;
		}
		return false;

	}
}

public class Levels : Player, IRewards
{
	public string Rewards(string reward)
	{
		return reward;
	}

	public string Battle(Player player, List<Character> enemies, int bonus, string reward, string specialAbility)
	{
	    Console.WriteLine("\nSpecial Ability for this level: " + specialAbility);
	    player.SpecialAbilityGeneration(specialAbility);
	    
		foreach (var enemy in enemies)
		{
			Console.WriteLine("Enemy Health: " + enemy.Health);
			while(enemy.Health > 0)
			{
				player.ChooseAction(enemy, bonus);
				if(enemy.Health > 0)
				{
					enemy.Attack(player);
					if (player.Health <= 0)
					{
						Console.WriteLine("\nGame Over! Player Defeated.");
						Environment.Exit(0);
					}
				}
			}
			player.EnemyDied();
		}
		return reward;
	}

	//This Method defines the 1st level where player encounters 1 enemy and the enemy should be dead in order to proceed to 2nd level
	public void Level1(Player player)
	{
		Character enemy = new Character{Health = 60};

		Console.WriteLine("Level 1");
		Console.WriteLine("\nPlayer spotted 1 enemy");
		Console.WriteLine("\nPlayer Health: " + player.Health);


		string rewardLevel1 = Battle(player, new List<Character> { enemy }, 0, "Map", "");

		Console.WriteLine("\nLevel 1 Cleared. Congratulations!!!");
		Console.WriteLine("Reward Achieved: " + rewardLevel1);
		Console.WriteLine("Player got a map of the area which will be helpful in navigation");
		Console.WriteLine("\n----------------------------------------------------------------");

	}

	//This Method defines the 2nd level where player encounters 2 enemies and the enemies should be dead in order to proceed to 3rd level
	public int Level2(Player player)
	{
		Console.WriteLine();
		player.Health = 110;// before proceeding to next level the health of the player renews or resets to the maximum health
		Console.WriteLine("Level 2");
		Console.WriteLine("Player is headed to the 2nd level where 2 enemies are spotted");
		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();
		Console.WriteLine("\n\nPlayer Health: " + player.Health);

		string rewardLevel2 = Battle(player, new List<Character>
		{
			new Character { Health = 80 },
			new Character { Health = 100 }
		}, 0, "Sword", "Critical Hits");

		Console.WriteLine("\nLevel 2 Cleared. Congratulations!!!");
		Console.WriteLine("Reward Achieved: " + rewardLevel2);
		Console.WriteLine("Player got a sword which helps in boosting melee damage (+20)");
		Console.WriteLine("\n----------------------------------------------------------------");

		int sword = 20;
		return sword;
	}

	//This Method defines the 3rd level where player encounters 3 enemies and the enemies should be dead in order to proceed to 4th level
	public int Level3(Player player, int swordPower)
	{
		Console.WriteLine();
		player.Health = 120;// before proceeding to next level the health of the player renews or resets to the maximum health
		Console.WriteLine("Level 3");
		Console.WriteLine("Player spotted 3 enemies");
		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();
		Console.WriteLine("\n\nPlayer Health: " + player.Health);

		string rewardLevel3 = Battle(player, new List<Character>
		{
			new Character { Health = 100 },
			new Character { Health = 110 },
			new Character { Health = 120 }
		}, swordPower, "Shield", "Blocker");

		Console.WriteLine("\nLevel 3 Cleared. Congratulations!!!");
		Console.WriteLine("Reward Achieved: " + rewardLevel3);
		Console.WriteLine("This reward helps the player to improve defense");
		Console.WriteLine("\n----------------------------------------------------------------");

		int shield = 20;
		return shield;
	}

	//This Method defines the 4th level where player encounters 4 enemies and the enemies hould be dead in order to proceed to 5th level
	public int Level4(Player player, int shieldPower)
	{
		Console.WriteLine();
		player.Health = 140;// before proceeding to next level the health of the player renews or resets to the maximum health
		Console.WriteLine("Level 4");
		Console.WriteLine("Player spotted 4 enemies");
		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();
		Console.WriteLine("\n\nPlayer Health: " + player.Health);

		string rewardLevel4 = Battle(player, new List<Character>
		{
			new Character { Health = 90 },
			new Character { Health = 110 },
			new Character { Health = 120 },
			new Character { Health = 160 }
		}, shieldPower, "Armour", "Life Steal");

		Console.WriteLine("\nLevel 4 Cleared. Congratulations!!!");
		Console.WriteLine("Reward Achieved: " + rewardLevel4);
		Console.WriteLine("This reward helps the player to enhance overall defence and survivability");
		Console.WriteLine("\n----------------------------------------------------------------");

		int armour = 50;
		return armour;
	}

	//This Method defines the 5th level where player encounters 5 enemies and the enemies should be dead in order to proceed to 6th level
	public int Level5(Player player, int armourPower)
	{
		Console.WriteLine();
		player.Health = 160;// before proceeding to next level the health of the player renews or resets to the maximum health
		Console.WriteLine("Level 5");
		Console.WriteLine("Player spotted 5 enemies");
		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();
		Console.WriteLine("\n\nPlayer Health: " + player.Health);

		string rewardLevel5 = Battle(player, new List<Character>
		{
			new Character { Health = 100 },
			new Character { Health = 120 },
			new Character { Health = 140 },
			new Character { Health = 180 },
			new Character { Health = 200 }
		}, armourPower, "Bow", "Ranged Attack");

		Console.WriteLine("\nLevel 5 Cleared. Congratulations!!!");
		Console.WriteLine("Reward Achieved: " + rewardLevel5);
		Console.WriteLine("This reward increases ranged damage");
		Console.WriteLine("\n----------------------------------------------------------------");

		int bow = 50;
		return bow;
	}


	//This method defines the 6th and the last level of this game where the player confronts the boss of the monstrous creatures "Murlocs" who himself holds many special abilities
	public void Level6(Player player, int bowPower)
	{
		Console.WriteLine();
		player.Health = 200;// before proceeding to next level the health of the player renews or resets to the maximum health
		Console.WriteLine("Welcome to the final level: Level 6");
		Console.WriteLine("\nPlayer confronts the Boss MURLOCS");
		Character Murlocs = new Character{Health = 400};
		Console.WriteLine("Description about Murlocs: ");
		Console.WriteLine("\nPowerful monster with unique abilities like \n1. Ground Slash: Area-of-effect damage, \n2. Speed Dash: Quick attacks that bypass defences and \n3. Health Regeneration: Gradually restores its HP during battle.");
		Console.WriteLine("\nPress any key to start the game: ");
		Console.ReadKey();
		Console.WriteLine("\n\nPlayer Health: " + player.Health);
		Console.WriteLine("Murlocs Health: " + Murlocs.Health);


		while(Murlocs.Health > 0 && player.Health > 0)
		{
			Random random = new Random();
			int enemyAction = random.Next(1,4);

			switch(enemyAction)
			{
			case 1:
				Console.WriteLine("Murlocs activated the ability Ground Dash:  Area-of-effect damage");
				player.TakeDamage(50);
				break;

			case 2:
				Console.WriteLine("Murlocs activated the ability Speed Dash:  Quick attacks that bypass defences.");
				Defense = 0;
				player.TakeDamage(20);
				break;

			case 3:
				Console.WriteLine("Murlocs activated the ability Health Regeneration:  Gradually restores its HP during battle.");
				Murlocs.Health += 20;
				player.TakeDamage(20);
				break;
			}
			player.ChooseAction(Murlocs,bowPower);
			if (player.Health <= 0)
			{
				Console.WriteLine("\nGame Over! Player defeated final level.");
				Environment.Exit(0);
			}
		}


		Console.WriteLine("Congratulations!!!");
		Console.WriteLine("Player finally defeated the boss Murlocs");
		Console.WriteLine("The village is protected and free from any evil creatures now.");
		Console.WriteLine("\n----------------------------------------------------------------");


	}

}
















