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

## Launching Pipeline
Include the following in your launch file.
```
<launch>
    <include file="$(find pc_pipeline_launch)/launch/pc_pipeline.launch">
	<arg name="pc_filter/xpassthrough/filter_limit_min" value="0" />
	<arg name="pc_filter/ypassthrough/filter_limit_min" value="0" />
	<arg name="pc_filter/zpassthrough/filter_limit_min" value="0" />
	<arg name="pc_filter/xpassthrough/filter_limit_max" value="1" />
	<arg name="pc_filter/ypassthrough/filter_limit_max" value="1" />
	<arg name="pc_filter/zpassthrough/filter_limit_max" value="1" />
	<arg name="pc_filter/observed_frame_id" value="/camera_rgb_optical_frame" />
	<arg name="pc_filter/filtered_frame_id" value="/ar_marker_8_filtered" />
	<arg name="pc_filter/input_pc_topic" value="/camera/depth_registered/points" />
	<arg name="pc_filter/output_pc_topic" value="/filtered_pc" />
	<arg name="run_partial_mesh" value="True" />
    </include>
</launch>
```

## Step by Step Sanity check:

1) Verify the raw pointcloud is publishing in RVIZ:
<img src="https://github.com/CURG/pc_pipeline_launch/blob/master/imgs/raw_pointcloud.png" alt="raw pointcloud"
         title="Raw PointCloud" align="right" />
	 
	 
2) Verify that the reference frame you want to filter the raw pointcloud in exists. Here we are going to filter our cloud in the frame of the AR marker tag using the package from https://github.com/ros-perception/ar_track_alvar.  We also smooth the detected tf frame using https://github.com/CURG/filter_tf. The below image shows that these ar tags are being detected correctly. 
<img src="https://github.com/CURG/pc_pipeline_launch/blob/master/imgs/filter_frame.png" alt="raw pointcloud"
         title="Raw PointCloud" align="right" />
	 
	 
3) If the filtered frames our detected, we can verify that pc_filter is able to filter the raw pointcloud in the desired reference frame using the parameters from the launch file.  In our example, the pointcloud is published in the frame of reference of the ar tag (/ar_marker_8_filtered), and the newly filtered pointcloud is published on the ROS topic /filtered_pc as shown below.
<img src="https://github.com/CURG/pc_pipeline_launch/blob/master/imgs/pc_filter.png" alt="raw pointcloud"
         title="Raw PointCloud" align="right" />
	 
	 

