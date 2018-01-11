# 第二十六章 Main组件

![](/assets/26/c26.png)

在每个系统里，都至少有一个这样的组件，创建，协调和监督其他组件，我称这种组件为Main组件。

## 终极细节

Main组件是最终细节，即最低层级的策略。这是系统的初始入口点。除了操作系统，什么都不依赖于它。它的任务是创建所有的工厂，策略和其他全局设施，然后把控制权交给系统的高层抽象部分。

在这个Main组件中，依赖关系应该由依赖注入框架注入。一旦它们被注入到Main中，Main就会正常地分配这些依赖关系，而不使用框架。

把Main当作所有脏组件中最脏的。[^1]

考虑一下最新版本的寻找Wumpus中的以下主要组件。注意它是如何加载所有我们不希望代码的主体知道的字符串的。

```Java
public class Main implements HtwMessageReceiver {
    private static HuntTheWumpus game;
    private static int hitPoints = 10;
    private static final List<String> caverns = new ArrayList<>();
    private static final String[] environments = new String[]{
        "bright",
        "humid",
        "dry",
        "creepy",
        "ugly",
        "foggy",
        "hot",
        "cold",
        "drafty",
        "dreadful"
    };
    private static final String[] shapes = new String[] {
        "round",
        "square",
        "oval",
        "irregular",
        "long",
        "craggy",
        "rough",
        "tall",
        "narrow"
    };
    private static final String[] cavernTypes = new String[] {
        "cavern",
        "room",
        "chamber",
        "catacomb",
        "crevasse",
        "cell",
        "tunnel",
        "passageway",
        "hall",
        "expanse"
    };
    private static final String[] adornments = new String[] {
        "smelling of sulfur",
        "with engravings on the walls",
        "with a bumpy floor",
        "",
        "littered with garbage",
        "spattered with guano",
        "with piles of Wumpus droppings",
        "with bones scattered around",
        "with a corpse on the floor",
        "that seems to vibrate",
        "that feels stuffy",
        "that fills you with dread"
    };
```

现在这里有个main函数。注意它是如何使用HtwFactory创建游戏的。它以类名htw.game.HuntTheWumpusFade传递，因为这个类比Main更脏。这可以防止该类中的更改导致Main重新编译/重新部署。

```Java
public static void main(String[] args) throws IOException {
    game = HtwFactory.makeGame("htw.game.HuntTheWumpusFacade",
            new Main());
    createMap();
    BufferedReader br =
        new BufferedReader(new InputStreamReader(System.in));
    game.makeRestCommand().execute();
    while (true) {
        System.out.println(game.getPlayerCavern());
        System.out.println("Health: " + hitPoints + " arrows: " +
            game.getQuiver());
        HuntTheWumpus.Command c = game.makeRestCommand();
        System.out.println(">");
        String command = br.readLine();
        if (command.equalsIgnoreCase("e"))
            c = game.makeMoveCommand(EAST);
        else if (command.equalsIgnoreCase("w"))
            c = game.makeMoveCommand(WEST);
        else if (command.equalsIgnoreCase("n"))
            c = game.makeMoveCommand(NORTH);
        else if (command.equalsIgnoreCase("s"))
            c = game.makeMoveCommand(SOUTH);
        else if (command.equalsIgnoreCase("r"))
            c = game.makeRestCommand();
        else if (command.equalsIgnoreCase("sw"))
            c = game.makeShootCommand(WEST);
        else if (command.equalsIgnoreCase("se"))
            c = game.makeShootCommand(EAST);
        else if (command.equalsIgnoreCase("sn"))
            c = game.makeShootCommand(NORTH);
        else if (command.equalsIgnoreCase("ss"))
            c = game.makeShootCommand(SOUTH);
        else if (command.equalsIgnoreCase("q"))
            return;

        c.execute();
    }
}
```

注意main函数创建了输入流，包含了游戏的主循环，解析简单的输入指令，但随后移交给其他更高层的组件处理。

最后，注意main创建的map。

```Java
    private static void createMap() {
        int nCaverns = (int) (Math.random() * 30.0 + 10.0);
        while (nCaverns-- > 0)
            caverns.add(makeName());
            for (String cavern : caverns) {
                maybeConnectCavern(cavern, NORTH);
                maybeConnectCavern(cavern, SOUTH);
                maybeConnectCavern(cavern, EAST);
                maybeConnectCavern(cavern, WEST);
        }

        String playerCavern = anyCavern();
        game.setPlayerCavern(playerCavern);
        game.setWumpusCavern(anyOther(playerCavern));
        game.addBatCavern(anyOther(playerCavern));
        game.addBatCavern(anyOther(playerCavern));
        game.addBatCavern(anyOther(playerCavern));

        game.addPitCavern(anyOther(playerCavern));
        game.addPitCavern(anyOther(playerCavern));
        game.addPitCavern(anyOther(playerCavern));

        game.setQuiver(5);
    }
    // much code removed…
}
```

重点在于，Main是干净架构最外层的脏的低层级模块。它将所有内容加载到高级系统中，然后手动控制它。

## 小结

将Main看作是应用程序的插件——一个插件，它设置初始条件和配置，收集所有外部资源，然后将其控制交到应用程序的高层级策略。由于它是一个插件，所以可能有许多Main组件，每个应用程序的配置都有一个。

例如，你可以建一个开发的Main插件，另一个用于测试，另一个用于生产。你也可以为每个部署到的国家/地区，每个管辖区域或每个客户分配一个Main插件。

当你将Main作为插件组件考虑时，支撑架构边界，配置问题变得更容易解决。

[^1]: Think of Main as the dirtiest of all the dirty components.

