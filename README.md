# pc_pipeline_launch
ROS package with launch files for pointcloud processing pipeline

The pc_filter node listens for raw pointclouds from the kinect and filters them using a passthrough filter. pc_scene_completion is constantly maintaining a list of the most recent filtered pointclouds published by pc_filter. pc_scene_completion also exposes an action server for scene completion, when hit by a client, pc_scene_completion takes the 5 most recent filtered clouds, concatenates them, segments them, and runs the individual segments through either pc_partial_mesh or shape_completion_server_v2 to generate meshes from the partial pointclouds.  This list of meshes, their poses, and the partial_view pointcloud for each segment are all returned to the client. 

## Setup
```
cd ~/ros_ws
mkdir pc_pipeline
cd pc_pipeline
git clone git@github.com:CURG/pc_partial_mesh.git
git clone git@github.com:CURG/pc_pipeline_launch.git
git clone git@github.com:CURG/pc_filter.git
git clone git@github.com:CURG/pc_scene_completion.git
cd ~/ros_ws
catkin_make
```


