# CarND-Path-Planning-Project - Reflection
Self-Driving Car Engineer Nanodegree Program

<p align="center">
	<img width="650" height="450" src="https://github.com/SamH1/CarND-Path-Planning-Project/edit/master/15miles.PNG">
</p>

### Disclosure
The solution I implemented is highly based on the Project Walkthrough And Q&A video.

### Reflection on the Path Planning model
#### Path planner

- If the previous path (returned by the simulator) is empty, we start by creating two points that are tangent to the current position of the car using the data returned by the simulator (car_x, car_y and car_yaw). else, we use the two last points from the previous path to make our new path starting points tangent to the previous one.
- Then we push to the path 3 (x,y) waypoints widely spaced (25m) that follow the car lane s coordinate.
- Then we fill our path with the remaining waypoints from the previous path that were not visited
- Then we fill up our path with more waypoints up to 55 points in total in our case. We use the spline function to calculate the y coordinate of each x and space our waypoints in a way that help us to drive on the desired speed using the formula (N*0.02*vel = d). where N is the number of waypoints, 0.02s is the frequency, vel the velocity of our car and d the distance between the first and last point on the path. 

#### Acceleration and Jerk limitation
- In order to respect acceleration and  jerk limitations, we start with a reference velocity of 0 mph then we incrementally increase the velocity by 0.224 mph at each message event (0.02s) this corresponds to an acceleration of 5m/s². When we need to break to avoid a crash, we also decelerate by 5m/s² using the same method. For the Jerk and Acceleration due to quick change in direction, the spline function we use in the path planner contribute to smooth the trajectory and help reducing jerk when changing direction

#### Lane change
- At each simulator message, we read sensor fusion data to loop over all the cars surrounding our car and capture there coordinates and velocity.
- Based on these coordinates, when we detect a slower cat in front of us, we measure our distance to the car and flag a warning (too_close) when the distance is lower than 30m.
- Also based on these coordinates we check if we have clear space in left and right lane to change lane. A clear space means that front and back cars in the right and left lanes are at a safe distance. For each of these two lanes, if we have clear space we flag a variable that indicates that changing lane is safe. 
- At each simulator message, as long as the flag (too_close) still true, we reduce incrementally the speed at each iteration by 0.224 mph. At the same time we read the values of the flags indicating if the left lane or the right lane are clear. If one of them is clear (priority to the left lane), we update the value of the lane for our car with the corresponding lane index (0 left. 1 middle, 2 right) and the path planner previously described generates a smooth transition path to the new lane.
- When there is no (too_close) flag, the car will incrementally increase it's speed by 0.224 mph at each simulator message (0.02s) until attaining a target speed of 49.5 mph

### Idea for improvement:
Currently, the model will always prioritize a change to left lane when safety conditions are respected. We can improve the model by analysing the speed and distances of the surrounding cars (left and right lanes) to prioritize a lane change that will improve the forward progress. 





