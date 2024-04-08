using System;
using System.Collections.Generic;

// Interface for a tennis player
public interface IPlayer
{
    string Name { get; }
    int Wins { get; }
    void UpdateMatchResult(bool won);
}

// Interface for managing a tennis team
public interface ITeamManager
{
    void AddPlayer(string name);
    void SimulateMatch(string playerName, bool won);
    void DisplayRoster();
    IPlayer FindPlayerWithMostWins();
}

// Class to represent a tennis player
public class Player : IPlayer
{
    public string Name { get; }
    public int Wins { get; private set; }

    public Player(string name)
    {
        Name = name;
    }

    public void UpdateMatchResult(bool won)
    {
        if (won)
        {
            Wins++;
        }
    }
}

// Class to manage tennis teams
public class TennisTeamManager : ITeamManager
{
    private readonly List<IPlayer> players;

    public TennisTeamManager()
    {
        players = new List<IPlayer>();
    }

    public void AddPlayer(string name)
    {
        if (string.IsNullOrWhiteSpace(name))
        {
            throw new ArgumentException("Player name cannot be empty or whitespace.");
        }

        IPlayer player = new Player(name);
        players.Add(player);
    }

    public void DisplayRoster()
    {
        Console.WriteLine("Tennis Team Roster:");
        Console.WriteLine("-------------------");
        foreach (IPlayer player in players)
        {
            Console.WriteLine($"Name: {player.Name}, Wins: {player.Wins}");
        }
    }

    public void SimulateMatch(string playerName, bool won)
    {
        IPlayer player = players.Find(p => p.Name.Equals(playerName, StringComparison.OrdinalIgnoreCase));
        if (player != null)
        {
            player.UpdateMatchResult(won);
        }
        else
        {
            throw new ArgumentException($"Player '{playerName}' not found.");
        }
    }

    public IPlayer FindPlayerWithMostWins()
    {
        IPlayer playerWithMostWins = null;
        int maxWins = 0;

        foreach (IPlayer player in players)
        {
            if (player.Wins > maxWins)
            {
                maxWins = player.Wins;
                playerWithMostWins = player;
            }
        }

        return playerWithMostWins;
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Creating a TennisTeamManager object
        ITeamManager teamManager = new TennisTeamManager();

        try
        {
            // Adding players to the team
            teamManager.AddPlayer("Novak Djokovic");
            teamManager.AddPlayer("Serena Williams");
            teamManager.AddPlayer("Maria Sharapova");

            // Simulating matches and updating statistics
            teamManager.SimulateMatch("Novak Djokovic", true); // Novak won
            teamManager.SimulateMatch("Novak Djokovic", true); // Novak won
            teamManager.SimulateMatch("Serena Williams", false); // Serena lost
            teamManager.SimulateMatch("Maria Sharapova", true); // Maria won
            teamManager.SimulateMatch("Maria Sharapova", true); // Maria won

            // Displaying the roster of the team
            teamManager.DisplayRoster();

            // Finding the player with the most wins
            IPlayer playerWithMostWins = teamManager.FindPlayerWithMostWins();
            if (playerWithMostWins != null)
            {
                Console.WriteLine($"\nPlayer with the most wins: {playerWithMostWins.Name} (Wins: {playerWithMostWins.Wins})");
            }
            else
            {
                Console.WriteLine("\nNo player found.");
            }
        }
        catch (ArgumentException ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
