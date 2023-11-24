# Tech Academy Game Development Bootcamp Live Project

During our first Live Project in the Tech Academy Game Development Bootcamp, I collaborated with a team to craft an engaging arcade menu featuring a variety of thrilling games. My primary focus was on developing a unique rendition of the popular Temple Run game.

## Project Highlights:

- **Arcade Menu:** Our team successfully crafted a diverse arcade menu, showcasing a collection of captivating games for users to enjoy.

- **Temple Run Recreation:** I took on the challenge of creating my version of the popular Temple Run game, pushing the boundaries of my Unity and C# skills. This endeavor proved to be both exciting and rewarding, as it prompted me to explore and acquire new capabilities within these technologies.

- **Problem Solving:** Throughout the project, I encountered numerous challenges that weren't immediately apparent. Leveraging the skills I've cultivated, I successfully devised creative solutions, further enhancing my problem-solving abilities.

## Temple Run Endless Runner

### Road Manager

To create the endless runner experience, I created the `TempleRun_RoadManager` class. This class manages the spawning and deletion of road pieces, ensuring a seamless and continuous gameplay experience. The endless runner experience was a new skill that I learned and was able to utilize. 

### Player Control

The endless runner wouldn't be complete without responsive player control. The `Jump()` coroutine and `OnTriggerEnter2D` method handle jumping mechanics and collisions. I wanted to emphasize this code as it enables me to selectively control the obstacles over which the player can jump. This customization empowers me to choose the specific obstacles for which the jump function is allowed.

## Explanation

### Temple Run Road Manager

The `TempleRun_RoadManager` class is responsible for managing the endless runner mechanics. It dynamically spawns and deletes road pieces, creating the illusion of an infinite path. The key methods include `SpawnRoad()`, `DeleteRoad()`, and others.

### Temple Run Player Control Code

#### Jump Mechanism

The `Jump()` coroutine provides a smooth jumping experience for the player. It handles the upward motion, pause at the peak, and gradual descent. 

#### Collision Handling

The `OnTriggerEnter2D` method manages collisions with enemies, walls (hole obstacles), and collectibles. It triggers actions such as losing health, collecting items, and modifying the player's speed.

## Team Collaboration:

Working collaboratively within a team setting significantly enriched my learning experience. Some notable benefits include:

- **Knowledge Reinforcement:** As a team, we deepened our understanding of Version Control, gaining proficiency in tools such as Github and Azure DevOps. This hands-on experience solidified our grasp of collaborative coding practices.

- **Diverse Perspectives:** Collaborating with peers brought diverse perspectives to the table, fostering a creative environment where innovative ideas flourished.

- **Shared Learning:** The team environment provided ample opportunities for knowledge exchange, allowing each member to contribute their unique skills and expertise.

## Conclusion:

The Live Project not only honed my technical skills but also instilled in me the importance of effective teamwork and collaborative problem-solving. This journey has been a cornerstone in my ongoing development as a game developer, and I'm excited to share our collective achievements with the broader community.

Thank you for checking out my Live Project!

## Temple Run Player Control

<a name="player-control"></a>

```csharp
IEnumerator Jump()
    {
        isJumping = true;

        float initialY = transform.position.y;
        float jumpEndTime = Time.time + jumpDuration;

        while (Time.time < jumpEndTime)
        {
            float t = 1 - Mathf.Pow((jumpEndTime - Time.time) / jumpDuration, 2);
            transform.Translate(Vector3.up * Mathf.Lerp(0, jumpHeight, t) * Time.deltaTime);
            yield return null;
        }

        transform.position = new Vector3(transform.position.x, initialY + jumpHeight, transform.position.z);

        yield return new WaitForSeconds(0.5f);

        while (Time.time < jumpEndTime + jumpDuration)
        {
            float t = Mathf.Pow((Time.time - jumpEndTime) / jumpDuration, 2);
            transform.Translate(Vector3.down * Mathf.Lerp(0, jumpHeight, t) * Time.deltaTime);
            yield return null;
        }

        transform.position = new Vector3(transform.position.x, initialY, transform.position.z);

        isJumping = false;
        playerAnimator.SetBool("JumpAnima", false);
    }

    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Enemy")) //enemy is spikes and enemy
        {
            LoseHealth();
            Debug.Log("LoseHealth triggered!");
        }
        else if (other.CompareTag("Wall") && !isJumping) //Wall is my 'hole' obstacle
        {
            LoseHealth();
            Debug.Log("LoseHealth triggered!");
        }
        else if (other.CompareTag("Collectable"))
        {
            IncreaseScore(10);
            IncreaseSpeed(0.25f); // change speed increase
            Destroy(other.gameObject);
        }
    }
```
## Temple Run Road Manager

<a name="road-manager"></a>

```csharp
public class TempleRun_RoadManager : MonoBehaviour
{
    public GameObject[] roadPrefabs; // Array of road prefabs
    public Transform player;  // Reference to the player or camera

    private float spawnX = 18.29f; // Initial spawn position
    private float roadLength = 18.25f; // Length of road prefab
    private int roadsOnScreen = 3; // Number of road pieces to keep on the screen

    private List<GameObject> activeRoads;

    private void Start()
    {
        activeRoads = new List<GameObject>();
        SpawnRoad();
    }

    private void Update()
    {
        // Check if need to spawn a new road piece
        if (player.position.x + roadLength * roadsOnScreen > spawnX + roadLength)
        {
            SpawnRoad();
            if (activeRoads.Count > roadsOnScreen)
            {
                // Delay the deletion of the very last road piece by 5 seconds
                Invoke("DeleteRoad", 3f);
            }
        }
    }

    private void SpawnRoad()
    {
        // Randomly select a road prefab from the array
        GameObject selectedRoadPrefab = roadPrefabs[Random.Range(0, roadPrefabs.Length)];

        // Instantiate the selected road prefab
        GameObject road = Instantiate(selectedRoadPrefab, new Vector3(spawnX, -7.34f, 0), Quaternion.identity);

        // Add the instantiated road to the activeRoads list
        activeRoads.Add(road);

        // Adjust the offset for the next road piece
        spawnX += roadLength;
    }

    private void DeleteRoad()
    {
        // Remove the oldest road piece 
        Destroy(activeRoads[0]);
        activeRoads.RemoveAt(0);
    }
}
```
