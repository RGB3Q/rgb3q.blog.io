以下文字为对2020 GDC AI Navigation: It's not a solved problem, but a solved problem 讲座上James Anhalt对星际2寻路技术的介绍内容整理。

I'm James Holton and lead software engineer on game systems for the StarCraft two team and I was the dedicated pathing software engineer for the whole project

I was working on it at the time that start up to started and they asked me to be the dedicated programmer after fighting fires on Diablo two and Warcraft three for a while. I was I was eager to be on a game team again and our requirements were as follows ：

Hundreds of units had to be able to pass, and that called for obviously very fast solution.

And we want to improve on previous tiles so the day after I accepted my lead, comes in and pops this 20 page document down with all the things I want to improve from previous games and after about seven years, I think we did a pretty good job.



In the pathing game, usually no news is good news. It's kind of a thankless job and if it works well, nobody really notices, if it doesn't work well it's always your fault. so it's kind of nice to read on some blogs that people actually like the new pathing in star craft 2 in fact, some people actually complain that it was too good in some cases.

They actually wanted the bad behaviors from previous games because it took a greater I'm out of skill to overcome those so you can't please everyone I also read a lot of speculation about how it all worked from whether was it flow fields was the steering and what not so l'd say the solution is a very integral part of the game and it really only consist of three parts: we plan we steer and we collide. 



So we represent the world with the constrained Delaney triangulation and this is a huge departure from our previous games, which had generally used a regular grid like a Warcraft 3. with a grid it's nice, things are simple. Everything's order one to look up, you can represent dynamic moving objects easily in that grid and update them quickly.

But square cells didn't really fit well with what I actually saw in star craft one which was lots of diagonal ramps and an isometric view of the world, and two actually represent the diagonals to the quality level that we would need we'd actually need to have maps of the size of ranging from 1024 x 1024, and I felt up that that was really a large amount of memory and would just slow us down since nowadays, memory is much slower than CPUs.



Like I said, the Delaney triangulation allowed for true diagonals and also allowed the designers later on to round off our buildings with with diagonals allow units to pass between them. One problem with a triangulation is actually locating yourself in that triangulation. for this we used the cashed jump and walk, where I overlaid a sparse regular grid on the triangulation and starting with a cashed triangle I walked the mash towards the destination triangle, and then after finding that triangle, I reinserted that into the cash for next time and this worked exceptionally well. It was very fast a lot of our Ray cast, and look up for a localized around units and so and our only cast for a short distances so there was a lot of repeated use of that cash.



Another thing we had to pay attention to was that every point look up needed to return the exact same triangle, no matter what no matter which direction it came from no matter when you called it, whether it was during the simulation of the game or asynchronously and in UI code and that's because we run appeared to appear synchronous game where the simulation on everybody's computer has to be exactly the same

To make things quick, we statically allocate everything and try to condense the structures as small as possible. We only have 16 bites for every triangle face and only four bites for vertex and that allows us to have 64, 000 faces and 32, 000 vertices.

We have a index for each of the neighboring faces and an index for each vertex and we also have one bits that tells whether or not each edge is constrained or not and then we have another four bites of packing information like what's the train type



We build this description from rebuild this match from description of the maps, cliffs and ramps at load time. We basically build a tile representation of the world where each tile is representative eight triangles per cell and we scan that horizontally vertically and diagonally in every direction whenever there's different train types straddling one of those lines we insert those constraints into the mesh and after that all of our duel-dads and buildings in the game polygon footprints so we insert those into the mesh and then we go ahead and we cash that initial state off and we use that for whenever buildings are destroyed or removed during the game. We remember all the buildings that are destroyed on a single game loop and then just swap in the cashed padding mesh and reinsert all the active buildings and that worked pretty well. I mean it seems like it would be slow, but in our game buildings die fairly infrequently, and when they do, it's usually after large battles where units themselves have been killed.



Given that initial state, we also do some analysis of it to find islands in the game so that we can answer queries like are these two points ever connected at all, and the Al uses that extensively, and another departure from our previous games is that no mobile units are in the palish at all.We don't a-start around mobile units at all, and I figured people don't a star around other people when they're moving through a crowded subway system for example why do our units need to do that?

So we do basic a star search on this on the mesh and the mesh has a lot of a lot of winds over it sale base system. We only have about 2 000 triangles on average in our maps versus up to 6 4, 000 and the cell base solution and triangles have minimal fan out compared to three exits for squares, three or seven exits for squares. In fact since most of our triangles have at least one constrained edge. We only have we only have one exit per triangle, and that allows us to zip down corridors really quickly.



We do most of all the common optimization: priority queues, delayed clearing of used sets, and those things are found in most fast a-star implementations——again, no dynamic memory at all. and we only have four basic train types, which determine how a unit can walk from cell to cell and those are ground cliff buildings and no padding or none, but we do have many virtual train types that we mapped to these basic for train types. for example, we tag every building in the game with who owns that building, that way that I can use that to quarry how do I get from here to the enemies basin? Ignore all the enemies buildings. We also have a goal type where we tag so if we want to move a unit to an actual building, we can tag his footprint with the goal type and use that during the star search.



Additionally, the system paid off pretty well. We also use it for Al danger pathing, where we can insert octagons that represent the vision range of units into the pathing mesh also, and use that to path around the vision of enemy units that the Al actually knows about. we support pathing with different radi through the mesh, because we have our game supports multiple units with different radius. traditionally people might actually path like a point and have different pathing meshes per unit size, but since we support the user modified maps and user you know created units, we can't really afford to create different representations for every possible size.



Once we have a list of the triangles that we go through, we use the funnel algorithm to find the shortest path, and reduce space further we only stored the actual two next steps in the path per unit in case the world changes or units get bumped out of the way. They're gonna need to re-path anyway, and here's an example of the funnel algorithm here you see the funnel that the unit is needs to path through and what we do as we expand every vertex with the radius of the unit that's gonna pass through, and then we run the funnel algorithm to create a nice smooth shortest path through that.



Steering 

steering probably required the most work throughout the project. it's basically inspired by the Craig Reynolds did on Boyds, and we do all the following steering types of steering following both both target units and the created path from a-star: flocking grouping separation avoidance arrival, there's probably a few that I even forgot.

All these are pretty standard except for arrival, and here's an example of our flocking behavior

So these are air units that flock around in a group and if you consolidate them into a single point, they automatically separate smoothly.



Avoidance

avoidance deserves its own slide. There was a lot of iteration throughout the project to find something that worked well, and what we won't go through all the things that we ended up throwing away, but what we ended up with was basically we need to figure out who you could avoid, who you could ignore, all units moving in your same general direction, we ignore, all units that are idle that you'll be able to push out of the, way we ignore.

And so that cuts down the number unit significantly, and what we do is, I tried to think of what humans do when they walk around, and basically we create a site horizon, and we plot all of our obstacles on that horizon, and we aim for the gaps, obviously if the sinner is clear, we go down the sinner if the sinner is not clear, we go down the gap that's closest to the sinner. We do a couple of extra checks since this process does not look at the pathing mesh at all, I do a check to make sure that there's no walls in the way on the side of the side that I'm going to turn to so l don't get put into a wall and we also read the minds of units that are traveling towards us to see if they've already decided to avoid me in a certain direction and if so, I avoid them in the same direction. So we prevent the whole dance of which way do I go as I'm walking down the hallway?

Here's an example of our avoidance behavior, and you can see that it can actually navigate around concave depressions of stationary units.



Collision

So the last part of our solution involves collision, Another departure of our units were allowed to push other units. I figured moving in the straight line is always the fastest way to get somewhere and that's what the player wants when he tell someone to go somewhere.

there's a whole set of rules that evolved over the course of the project involving friendly versus enemy units who gets to push you when we encounter an idle unit that's friendly the idle units the one that gets pushed out out of the way when we encounter another moving unit, the faster unit gets pushed out of the way more than the slower unit.Conversely, if you meet an enemy unit, typically you both get pushed out out of the way

Moving stationary units if you have units on hold position, you never want units to pass through them so we need to make double ensure that units don't do that and here's an example of our pushing behavior with a pack of Zlin running with one guy running through as you can see once we do that it's it makes the a star problem go away completely and we don't even have to run A-star

And here with our bug display on, you can see that how the collision kinda you know, percolates out as guys turn red when they're in collision

And if you watch the earlier talk today bugging displays critical to getting the stuff to work and knowing what's going on

So with their collision, we basically run a physic simulation. All of our mobile units are our simulated as circles and it's all done using fix point math. We do that because our game asynchronous and needs to simulate exactly on every every different CPU out there and we found that floating point is not always the same. We do a limited number of rounds of collision and resolving with a special round at the end for stationary units like I said so that we can ensure that fast units don't pass through stationary units.

And we reuse the pathing mesh to collide those units with the world to make sure they don't penetrate cliffs and buildings and such

There are some other issues that came up throughout the project the reaper this was just a problem all around he jump jumps







There are some other issues that came up throughout the project th reaper this was just a problem unit all around he jumped cliffs and th main issue there was the a star code which determined when he was gonna jump a cliff was different from the behavior code that that the reaper locally ran to determine when he was gonna jump cliffs, and those were programmed by two different people, and if the a star said you could jump this cliff with the behavior codes that I couldn't then the reaper would get stuck and just last week I was still fixing some edge case issues with the reaper



Nearest neighbor searches, this is actually the slowest part of the solution was actually searching for units around you or during avoidance and the collision phase, and we do a lot of searches. We also do searches when we do target selection for determine which which enemies to kill and turns out that those two searches have different requirements and we were doing many more targeting searches, then padding searches so at the very end of the project we had to replace our solution with one that was better suited for targeting, but it actually slowed down the movement code, but the net when it was still in net wind, but it's something we may revisit in the future. 



Another issue is player intent. It's very difficult to tell what the player means when he clicks one point in the world is he telling is he trying to communicate that he wants his unit to go exactly to that point or is he trying to communicate that he wants to just go in that general direction and attack anything along the way generally we didn't really solve this issue, but we aired on the side of move exactly to the point where the player told him to go.



And lastly, there's a issue with Fog war and visiog if you take vision into account while you're padding say to an area where you saw a building at one time but you don't currently see it if you take vision account and you have a arranged unit you don't know if you're gonna be able to see that unit when you get to within range and so at that point if you try to path with a male unit, didn't try to get close as possible you're gonna get too close and if you take vision new account, you may flip-flop back-and-forth between those two states and never actually get to a place where you can attack the unit so we did the solve that was as soon as you get to within range of the unit if you can't see it, you switched to Malay mode and you try to get as close as you can and we never switched back until you get a new order



Closing thoughts always always the solution to the game. The Delaney triangulation represented the world much better with fewer nodes in the graph so it provided a big win on a star. We were a fast paced game and units die quickly so there was no need to put units in the actual patty mesh so we didn't have to update the mesh all the time.



And being stupid or not being stupid is better than being too smart. We didn't want units to take the long way around if there was one obstacle in the way it's better just to go forward and be responsive and if your solution is really laylored to the game you're likely to get some nice emergent behavior best example this is the zergling surround where if you have a pack of zergling and you tell to attack a large unit, they will literally surround that unit and golf it and take it down quickly.

This was not intentional, and in fact, the pro players complain that it made the game too easy because what they used to do what they used to have to do in. The previous star craft was actually moved beyond the unit. They wanted to attack and when their units were on both sides of the unit, then switched to attack the unit to take it down quickly.

And also, the CPU is fast and memory is slow, compact structures, no dynamic memory, huge winds for cash, coherency and making the solution work well thank

