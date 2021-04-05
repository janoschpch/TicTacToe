# TicTacToe
 
This is a TicTacToe Plugin based on the SpigotAPI. The plugin ensures that players can send each other a game request and then play the game TicTacToe. Also the plugin contains an API.

**Important!** The plugin is only tested in Minecaft Version 1.15.2

## Current Features

- Players can send match requests
- Players can accept or deny match requests
- TicTacToe Inventory with the team color, skulls and the tic tac toe field.
- TicTacToe queue command
- Commands: /match, /acceptmatch, /denymatch, /matchqueue

## Work in progress Features

- Admin command to create Matches between 2 players

## API
The Plugin also contains an API with 3 Bukkit Events and a easy to use API class.

```java
import de.magicccrafter.tictactoe.api.event.TicTacToeMatchCancelEvent;
import de.magicccrafter.tictactoe.api.event.TicTacToeMatchEndEvent;
import de.magicccrafter.tictactoe.api.event.TicTacToeMatchStartEvent;
import de.magicccrafter.tictactoe.utils.TicTacToeMatch;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class APIExample implements Listener {

    public void createMatchAndStopAfterTime(Player player1, Player player2, Integer seconds) {
        //Create and start TicTacToe match
        TicTacToeMatch match = TicTacToe.getInstance().getTicTacToeAPI().createAndStartMatch(player1, player2);

        //Broadcast a message to admins
        Bukkit.broadcast("An match has been created between " + player1.getName() + " and " + player2.getName() + " it stops in " + seconds + " seconds", "plugin.announcements");

        //Stop match after x seconds
        Bukkit.getScheduler().runTaskLater(TicTacToe.getInstance(), new Runnable() {
            @Override
            public void run() {
                //Stop match
                TicTacToe.getInstance().getTicTacToeAPI().stopMatch(match);
                //Broadcast a message to admins
                Bukkit.broadcast("The match has been stopped", "plugin.announcements");
            }
        }, seconds * 20);
    }

    //TicTacToeMatchCancelEvent executes if a player left the game or closed the inventory
    @EventHandler
    public void onMatchCancel(TicTacToeMatchCancelEvent event) {
        event.getMatch().getPlayerA().sendMessage("the match has been canceled");
        event.getMatch().getPlayerB().sendMessage("the match has been canceled");
    }

    //TicTacToeMatchEndEvent executes when the match ends
    @EventHandler
    public void onMatchEnd(TicTacToeMatchEndEvent event) {
        //check if nobody wins
        if(event.isTie()) {
            //if is tie, winner and loser are null
            event.getMatch().getPlayerA().sendMessage("the match is a tie");
            event.getMatch().getPlayerB().sendMessage("the match is a tie");
            return;
        }
        event.getWinner().sendMessage("you won the match");
        event.getLoser().sendMessage("you lost the match");
    }

    //TicTacToeMatchStartEvent executes if a match has been started
    @EventHandler
    public void onMatchStart(TicTacToeMatchStartEvent event) {
        Bukkit.broadcastMessage("A tictactoe match has been started");
    }

}
```
