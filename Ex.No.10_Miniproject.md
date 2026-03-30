# Ex.No: 10  Implementation of 2D/3D game in Unity
### DATE: 23/3/26                                                                           
### REGISTER NUMBER : 212224043002
### AIM: 
To develop a game A* Pathfinding Algorithm Simulation in Unity 
### Algorithm:
```
1. Initialize the Scene
Identify the start node and target node in the graph.
2. Create Lists
Create two lists:
OPEN list (nodes to be explored)
CLOSED list (already visited nodes)
3. Add Start Node
Add the start node to the OPEN list.
Loop Until Path Found
Repeat the following steps:
4. Select Node
Choose the node with the lowest f(n) value from the OPEN list.
5. Move Node
Move the selected node from OPEN list to CLOSED list.
6. Check Goal
If the selected node is the target node:
→ Stop the process
→ Path is found
7. Explore Neighbors
For each neighboring node:
8. Calculate:
g(n) = distance from start
h(n) = estimated distance to target
f(n) = g(n) + h(n)
9. Update OPEN List
If neighbor is not in OPEN or CLOSED:
Add it to OPEN list
Set current node as its parent
10. Check Better Path
If neighbor is already in OPEN with higher cost:
Update its cost
Change its parent to current node
11. Repeat Process
Continue steps 5–10 until the target is reached.
12. Trace Path
Trace back from target node to start node using parent links.
13. Return Path
The shortest path is obtained.
14. End the Program
```  
### Program:
Node.cs
```
using System.Collections.Generic;
using UnityEngine;

public class Node : MonoBehaviour
{
    public List<Node> neighbors = new List<Node>();

    public float gCost; // distance from start
    public float hCost; // heuristic distance
    public float fCost => gCost + hCost;

    public Node parent;
}
```

Graph.cs
```
using System.Collections.Generic;
using UnityEngine;

public class Graph : MonoBehaviour
{
    public Node startNode;
    public Node targetNode;

    List<Node> openList = new List<Node>();
    List<Node> closedList = new List<Node>();

    public List<Node> FindPath()
    {
        openList.Add(startNode);

        while (openList.Count > 0)
        {
            Node currentNode = openList[0];

            // Find node with lowest fCost
            foreach (Node node in openList)
            {
                if (node.fCost < currentNode.fCost ||
                   (node.fCost == currentNode.fCost && node.hCost < currentNode.hCost))
                {
                    currentNode = node;
                }
            }

            openList.Remove(currentNode);
            closedList.Add(currentNode);

            // If reached target
            if (currentNode == targetNode)
            {
                return RetracePath(startNode, targetNode);
            }

            foreach (Node neighbor in currentNode.neighbors)
            {
                if (closedList.Contains(neighbor))
                    continue;

                float newCost = currentNode.gCost + Vector3.Distance(currentNode.transform.position, neighbor.transform.position);

                if (newCost < neighbor.gCost || !openList.Contains(neighbor))
                {
                    neighbor.gCost = newCost;
                    neighbor.hCost = Vector3.Distance(neighbor.transform.position, targetNode.transform.position);
                    neighbor.parent = currentNode;

                    if (!openList.Contains(neighbor))
                        openList.Add(neighbor);
                }
            }
        }

        return null;
    }

    List<Node> RetracePath(Node start, Node end)
    {
        List<Node> path = new List<Node>();
        Node current = end;

        while (current != start)
        {
            path.Add(current);
            current = current.parent;
        }

        path.Reverse();
        return path;
    }
}
```
TankMovement.cs
```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class TankMovement : MonoBehaviour
{
    public Graph graph;
    public float speed = 5f;

    List<Node> path;
    int targetIndex = 0;

    void Start()
    {
        path = graph.FindPath();
        StartCoroutine(FollowPath());
    }

    IEnumerator FollowPath()
    {
        if (path == null || path.Count == 0)
            yield break;

        Vector3 currentWaypoint = path[0].transform.position;

        while (true)
        {
            if (Vector3.Distance(transform.position, currentWaypoint) < 0.1f)
            {
                targetIndex++;
                if (targetIndex >= path.Count)
                    yield break;

                currentWaypoint = path[targetIndex].transform.position;
            }

            transform.position = Vector3.MoveTowards(transform.position, currentWaypoint, speed * Time.deltaTime);
            yield return null;
        }
    }
}
```
WaypointDebug.cs
```
using UnityEngine;

public class WaypointDebug : MonoBehaviour
{
    public Node node;

    void OnDrawGizmos()
    {
        if (node == null) return;

        Gizmos.color = Color.green;

        foreach (Node neighbor in node.neighbors)
        {
            if (neighbor != null)
            {
                Gizmos.DrawLine(transform.position, neighbor.transform.position);
            }
        }
    }
}
```
### Output:
<img width="1536" height="1024" alt="aifg" src="https://github.com/user-attachments/assets/a7d37862-ac3e-4ae3-8ad3-d05297907f48" />


### Result:
Thus the game A* Pathfinding Algorithm Simulation in Unity was developed.
