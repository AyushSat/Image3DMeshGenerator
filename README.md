# Image3DMeshGenerator

The aim of this project and its code is to take any array of images of a 3 dimensional object taken with structured illumination and construct a 3D rendering of the object.

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/3fdd114e-a695-4818-9777-6ea7672aa9d6)
###### Example of structured illumination

To start, 2 cameras were stationed in set spots. Then, pictures of a chessboard with known dimensions were taken, and I used OpenCV's findChessboardCorners function to calibrate the camera's intrinsic and extrinsic parameters, including 3D translation and rotation, focal length, and sensor center coordinates. I used **Matplotlib** to visualize our scene after calibration, resulting in this:

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/9e77eefd-ad12-42ce-b894-25a525f9d288)

Using Python and Numpy, I read in the images of the object in the background and the background without the image. By comparing each pixel with its counterpart, I constructed a mask of the pixels where the object was contained in. 

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/a71d78c0-8e86-4691-8f72-10323edbdf21)

Next, by using the images from structured illumination, each point of the object would have the same binary code of black and white stripes, regardless of camera POV. Using this code, I paired the points of the images together using numpy.intersect1d, resulting in a basic 3D point cloud.

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/e8db6095-4cc2-4c0e-9675-167c814186ec)

I implemented a boundary pruning algorithm to calculate the centroid of all the 3D point clouds, then removed any 3D points that were outside a certain range from the centroid to rid any extraneous points not screened out by the masks.

Now that points were filtered, I used scipy's Delaunay algorithm to generate a triangle mesh from the 3D point cloud. I then wrote 2 separate algorithms to correct the messy triangle data. I wrote a triangle pruning algorithm that sifted through the triangles and cut any triangle that was too large, as with a dense point cloud the triangles should be relatively small. Then, I implemented a mesh smoothing algorithm that associated triangles with it's neighbors, then brought it closer to its neighbors to smooth irregularities. The results were profound.

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/708eaf52-d323-4c8f-801b-dd43287e1586)

With all of the meshes created, I loaded in the .ply files into a program called MeshLab, and used a point based glueing algorithm to link meshes together in regards to orientation and position.

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/85bac8b6-f815-4300-9a57-73abf9a0429d)

The meshes were aligned, but very messy. After manually selecting and removing irrelevant point clouds and running a Poisson based surface reconstruction algorithm, I got a very smooth result:

![image](https://github.com/AyushSat/Image3DMeshGenerator/assets/18633636/72a7e0bd-4fd1-4839-b357-a19d5c967b69)

