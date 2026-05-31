Copy this resutls file and paste it into your own repo to showcase your results! Lab results do not count towards your score you'll receive for your submission, but we encourage you to show off what you learned in the lab!

<h1 align="center">AUTONOMOUS AI DRIVER WITH TORCS</h1>

<h3 align="center">IBM SkillsBuild TORCS Autonomous Driving Learning Lab, May 2026</h3>
                                   
<h5 align="center"> Participant- Aparnna Raju</h5> 

<h4>ABSTRACT</h4>

In this lab i worked hands‑on with TORCS to understand how an autonomous AI driver works and how small changes to control logic can significantly affect performance

<h4>LAB TASKS</h4>

🔷 TASK 1: Run the baseline AI driver

🔸What i did:
   -  Installed TORCS manually using Torcs.zip file
   - Set up the gym_torcs folder at `C:\RaceYourCode\gym_torcs\`
   - Created a Python virtual environment `torcs-env` and activated and run the main starter file `torcs_jm_par.py`
   - Launched TORCS and navigated to Race → Practice
   - Selected `scr_server` as the driver and started a New Race
   - Ran the baseline agent from PowerShell:


🔸Observation:
   -  Sucessfully extracted and implement Torcs file
   - Terminal shows `Client Connected on 3001...`
   - The car began moving on the track automatically without any human input
   - The car drove slowly and looked "robotic" at corners
   - Occasionally the car drifted toward the edge of the track
     
🔸 Ran the baseline agent from PowerShell:
```python
python torcs_jm_par.py
```
  
  Success✅ : Car moved without human controls and simulation ran without crashing.
     
------------------------------------------------------------------------------------------

🔷 TASK 2: Understand how the driver controls the car

   The AI driver follows a continuous loop every simulation step:~50 times per second

 🔸TORCS sends sensor data to the Python agent(`torcs_jm_par.py`) including:
   - `speedX` — current speed of the car
   - `angle` — angle of the car relative to the track
   - `trackPos` — distance from the center of the track
     
 🔸Then python code decides:
   - How much to steer,*Steering* - `calculate_steering()` uses `angle` and `trackPos`
   - Whether to accelerate, *Throttle* → `calculate_throttle()` compares current speed to `TARGET_SPEED`
   - Whether to brake, *Braking* → `apply_brakes()` triggers when `angle` exceeds `BRAKE_THRESHOLD`
   - When to change gears, *Gear* → `shift_gears()` changes gear based on speed thresholds
   - Commands send to TORCS and repeat
  
 🔸Key user configure parameters:
   | Parameter | value |  Uses |
   |---|---|---|
   | `TARGET_SPEED` | 100  | Target speed in km/h |
   | `STEER_GAIN` | 1  | How aggressively the car steers |
   | `BRAKE_THRESHOLD` | 0.1  | How strongly car stays centered |
   | `GEAR_SPEEDS` | [0, 20, 40, 80, 100, 180] | When to brake in corners |
   | `ENABLE_TRACTION_CONTROL` | True  |Toggle traction control system |
     
  The car does not learn —it follows logic rules defined by the parameters above. Small changes to these values directly change how the car drives.
  
  Success✅ : Understand how the cars works using parameters.

------------------------------------------------------------------------------------------

🔷 TASK 3: Make your first modifications

 🔸What i did:
   -  Make a change in TARGET_SPEED from 100 to 150
      
 🔸What I Observed:
   - The car went fast at begining — it showed a clear speed pattern:
   - On the begining the car swings right and left and little bit then moves and hit a wall, after hitting car slows speeds up
   - On straight sections car accelerated and sped up then slos down then speeds up
   - At approaching corners car slowed down before the bend ,through the corner car steered and reduced speed.
   - After exiting the corner → car sped up again on the straight
   - This slow → fast → slow cycle repeated continuously around the track
   - The car occasionally hit the wall because `BRAKE_THRESHOLD = 0.1` was braking too late before sharp corners
   - At higher speed of 150, the car struggled more to recover after corners compared to the baseline speed of 100

 🔸Why this happens:
   - The `apply_brakes()` function triggers when the track angle
   - exceeds `BRAKE_THRESHOLD`. At higher speed, the car reaches
   - corners faster but the braking threshold stayed the same, causing it to brake too late:

```python
def apply_brakes(S):
    return 0.3 if abs(S['angle']) > BRAKE_THRESHOLD else 0.0
```

  Conclusion:
  - Increasing `TARGET_SPEED` alone improved straight-line speed but reduced overall stability. Speed and control must bebalanced together.

  Success✅ : Modified parameters and ebserved changes.

  ------------------------------------------------------------------------------------------

🔷 TASK 4: Experiment and optimize (optional, exploratory)

  Experimenting with one parameter at a time
  
  🔸What i did:
    -  Changing one parameter value to find the changes
    -  observed and note down results
    -  find the best learning outcome
     
  🧪 Experiment 1 —  Adjust Steering Gain
```python
STEER_GAIN = 25
```
   Observed: Car corrected direction more aggressively.
             Steering felt more responsive but slightly jerky on straights.

  🧪 Experiment 2 —  Lower Brake Threshold
```python
BRAKE_THRESHOLD = 0.06
```
   Observed: Car started braking earlier before corners.
             Reduced the number of times the car went off track at bends.

  🧪 Experiment 3 —  Disable Traction Control
```python
ENABLE_TRACTION_CONTROL = False
```
   Observed: 
   - Car started moving but slowed down to **16 km/h**, Then sped up again while **swaying left and right**
   - Car could not hold a straight line without traction control
   - Eventually slowed down and **stopped completely at 03:10:05**
   - Without traction control the wheels spun freely causing the car to lose balance and direction completely

  🔸Best learning outcome:
```python
TARGET_SPEED = 130
STEER_GAIN = 25
BRAKE_THRESHOLD = 0.06
ENABLE_TRACTION_CONTROL = True
```
This gave the best balance of speed and stability on track.

  Result:
    -  Sucessfully changing one parameter to observe the changes
    -  Note down the result of each experiment
    -  Find the best learning outcome
  
  Success✅ : Successfully experiementing by changing parameter and observe changes.

  ------------------------------------------------------------------------------------------------------
  
🔷 TASK 5: Record results and reflect

  🔸Summary of parameter changes:
    | VALUE | TS (TARGET_SPEED) | SG (STEER_GAIN) | BT (BRAKE_THRESHOLD) | ETC (ENABLE_TRACTION_CONTROL) | OBSERVATION |
|---|---|---|---|---|---|
| ORIGINAL VALUE | 100 | 1 | 0.1 | True | Slow, robotic, stable |
| SPEED VALUE CHANGE | 150 | 1 | 0.1 | True | Faster but hit walls |
| EXPERIMENT 1 | 150 | 25 | 0.1 | True | Smoother steering |
| EXPERIMENT 2 | 150 | 25 | 0.06 | True | Earlier braking, fewer wall hits |
| EXPERIMENT 3 | 150 | 25 | 0.06 | False | Car swayed left and right, slowed and stopped|
| BEST VALUE | 130 | 25 | 0.06 | True | Best speed and stability |
    
  🔸One Thing That Surprised Me:
  
   When traction control was disabled, the car slowed down to **16 km/h**, then sped up again while 
   **swaying left and right** continuously. It could not hold a straight line and eventually stopped completely at **03:10:05**. This was unexpected —
   disabling one parameter caused the entire driving behaviour to break down completely.

  🔸One Thing I Would Try Next:
  
   I would experiment with `CENTERING_GAIN` to keep the car closer
   to the center of the track during high-speed corners, and also
   try adjusting `GEAR_SPEEDS` for more aggressive gear shifting.

  🎓Overall Reflection:
  
  This lab showed me that autonomous driving is not just about speed — it is about finding the right balance between all
  parameters working together. Even disabling one parameter like traction control completely changed the car behaviour from
  stable driving to uncontrolled swaying and stopping. This is exactly how real AI engineers work: change one thing, observe, and iterate.

---
*🏁 Lab completed as part of IBM SkillsBuild AI Builders Challenge — May 2026* 


  
