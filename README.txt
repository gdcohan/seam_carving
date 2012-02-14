README:
This project implements an algorithm for shrinking the width of an image by a given amount of pixels while minimizing distortions. It relies on a so-called "seam-carving" algorithm, in which we remove the vertical seam (one pixel from each row that is no more than one column away from the pixel being removed from the row above and below) with the lowest "energy". In order to do this in a less computationally intensive way, we eliminate all  possible seams at each row so that only the lowest possible seam involving a given pixel remains a possibility when the next row is examined. 

Further explanation of the algorithm can be found below. Further documentation of the project can be found at:
http://www.cs.brown.edu/courses/cs019/2011/assignments/seam_carving

General Algorithm:
There are three parts to my seam carving algorithm:

The first takes a 2d-list of pixels (as colors) and creates a corresponding 2d-vector with the energy of each pixel.To do this, I first create a 2d-vector the size of the image. Each row in the 2d-vector is then mutated to hold the brightness of each pixel--to do this, a function iterates through a row in the 2d-list of colors, sums the red, green, and blue values of each pixel, and uses that sum as the value for the corresponding index in the 2d-vector. This 2d-vector holding the brightness of each pixel is then used to create a 2d-vector holding the energy of each pixel. The energy formulas are applied to the appropriate neighbors of each pixel and the result is stored in a 2d "energy-vector".

The second part finds the lowest-importance seam in the image. It is based on the idea that the lowest-importance seam that includes a given pixel must also include the lowest-importance seam that includes a pixel above and adjacent to the given pixel. We can also think of this recursively. The base case is when the image is only one row--the lowest-importance seam is just the pixel with the lowest energy. After that, the lowest-importance seam is the seam that has the lowest importance when you add the energy of the pixel in the current row to the lowest-importance seam that is vertically adjacent to the previous row. 

This part of the program uses a 2d-vector of seam-nodes (make-seam-node cost path) to keep track of the lowest-importance seam upon encountering each row in the image (starting at the top of the image). The cost is the cumulative energy of each pixel in the path, and the path is a list of posns that keep track of the row and column of each pixel in that seam.

The final part of the algorithm is to remove the seam once it has been determined which is the lowest-importance. This involves removing the appropriate pixel from each row and then reinserting that row into a 2d-list of colors. 

Optimizations:

1) 
The optimization that increased the efficiency of the program the most was using local functions whenever I was calling a number of functions on the same 2d-vectors. Originally, each function was not locally defined and took one or two 2d-vectors as parameters. It turned out that doing this passing of vectors was slowing down the program so much that it was taking hours to run on an image that was about 300 * 500 pixels. To avoid this problem, I locally defined all the functions that needed to access the 2d-vectors. In this way, the vectors did not need to be passed as parameters which greatly improved the efficiency.

2) 
I decided to go from the 2d-list form of an image to a 2d-vector of the image (also replacing colors with brightnesses). Because so many calculations were going to be performed using each pixel, I think that converting the 2d-list to a 2d-vector improved the efficiency as it allowed for constant access time when calculating the energies and cost of seams. 

3)
I decided to create a 2d-vector of brightnesses as an intermediate step between the 2d-list of colors and a 2d-vector of energies. I did this to avoid calculating the brightness of the each pixel multiple times (because it will be a neighbor in calculating an x or y energy for multiple other pixels).

4)
Originally, I had my carve-one-seam function take an Image$ instead of a 2d-list of colors and process it completely. When I created the seam-carve function, though, I realized that I could convert the Image$ to a 2d-list of colors once, carve as many seams as I needed to, and then convert it back to an image. This saved me from having to convert back and forth between an Image$ and a 2d-list of colors once for each seam that was carved.

5) 
I changed the function that removed an element from a list to run more efficiently. Originally, I had the function set up to remove an index from a list, and then replace that list in a 2d-list. This meant reconstructing the 2d-list one time for each "row", each time updating one "row" in the 2d-list. In the final implementation, though, I reconstruct the new list as I go along. To do this, I remove an element from one row in the picture, and then cons that on to a recursive call to the same function. The only inefficiency that this introduced was that I had to reverse the list of pixels to remove that I accumulated during the processing functions. This was because the resulting list would have had as its first element the pixel to remove from the last row, whereas I needed the first element to be the pixel to remove from the first row.

6)
Originally, I had a function that calculated an x or y energy of a pixel given a list of brightnesses that corresponded to a neighboring pixel. However, I realized that I could just apply the formula to the neighboring pixels as I was accessing them without putting them into a list and then processing them with a separate function.
