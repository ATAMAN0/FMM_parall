<div id="top"></div>


<!-- PROJECT LOGO -->
<br />
<div align="center">
    <img src="https://user-images.githubusercontent.com/80456274/171564992-e377d833-c780-45aa-ab44-60c6b35c0425.png" alt="Logo" width="550" height="380">   
    <h2 align="center">FMM (fast multipole methode) </h2>
    <h3 align="center">Mini Projet</h3>
</div>

<!-- TABLE OF CONTENTS -->

  <summary>Table of Contents</summary>
  <ol>      
      <a href="#about-the-project">About The Project</a>         
  </ol>
  <ol>
    <li>
      <a href="#Part-I">Part I :</a>
         <ul>
              <li><a href="#"></a></li>
           </ul>
    </li>
        <li>
            <a href="#Part-II">Part II :</a>
            <ul> 
              <li><a href="#"></a></li>
            </ul>
           </li> 
        <li><a href="#Part-III">Part III :</a></li>
   </ol>



<!-- ABOUT THE PROJECT -->
# About The Project
This is an example of how you may give instructions on setting up your project locally.
To get a local copy up and running follow these simple example steps.

1. [**How to install visual studio code**](https://youtu.be/MlIzFUI1QGA)
2. **Clone the repo**
   ```sh
   git clone https://github.com/IlyasKadi/poisson-boltzmann.git
   ```
 
<p align="right">(<a href="#top">back to top</a>)</p>




# Part-I 
## FMM
The fast multipole method (FMM) is a numerical methodology designed to reduce the time it takes to calculate long-ranged forces in the n-body problem. It accomplishes this by employing a multipole expansion to enlarge the system Green's function, which allows one to group sources that are close together and treat them as if they were a single source .[1]

The FMM has also been used to speed up the iterative solver in the method of moments (MOM) as applied to computational electromagnetics problems.[2] The FMM is based on the multipole expansion of the vector Helmholtz equation and was first introduced in this way by Leslie Greengard and Vladimir Rokhlin Jr.[3]. The appropriate matrix elements are not required when employing the FMM to treat interactions between far-away basis functions.

## Project-IDEA
We worked on a code that concerns FMM implementation that computes electrostatic energy and the potential difference o between the nearest leaf cells, as well as the evaluation of the local expansion and All-pair calculation of the EP, and we were able to achieve a (94.8 %) improvement in speed and optimization (without losing the values computed).


# Part-II
## seq-code__vs__paralle-code

main-seq 

```C
int main(int argc, char **argv) {
/*----------------------------------------------------------------------------*/
  
  int j;
  double diff,max_diff = 0.0;

  double x = omp_get_wtime () ;
/*****************************************************************************/

  initialize();  /* Randomly generate particle positions & charges */
  mp_leaf();  /* Compute multipoles at the leaf cells */

  upward();  /* Upward pass to compute multipoles at all quadtree levels */
   
  downward();  /* Dowaward pass to compute local-expansion terms */
    
  nn_direct(); /* Evaluate potentials at all particle positions */

  all_direct();  /* All-pairs direct evaluation of potentials for validation */


  omp_set_num_threads(NUM_THREADS);

  #pragma omp  for schedule  (dynamic,CHUNK)  //num_threads (NUM_THREADS)
  for (j=0; j<Npar; j++) {
    diff = (pot[j]-pot_direct[j])/pot_direct[j];
    diff = diff>0 ? diff : -diff;
    max_diff = diff>max_diff ? diff : max_diff;
  }

  printf("\n===== Max potential difference = %e =====\n",max_diff);
  printf("===== Total  energy  = %e  =====\n",
    eng);

/*****************************************************************************/

double y = omp_get_wtime () ;

   printf("===== total time = %f =====\n\n",y-x);
  return 0;

}
```

main-parr
```C
int main(int argc, char **argv) {
/*----------------------------------------------------------------------------*/
  int j;
  double diff,max_diff = 0.0,tstart,tend;

  tstart = omp_get_wtime();

  /*****************************************************************************/
  initialize();  /* Randomly generate particle positions & charges */
  mp_leaf();  /* Compute multipoles at the leaf cells */
  upward();  /* Upward pass to compute multipoles at all quadtree levels */
  downward();  /* Dowaward pass to compute local-expansion terms */
  nn_direct();  /* Evaluate potentials at all particle positions */
  all_direct();  /* All-pairs direct evaluation of potentials for validation */

  for (j=0; j<Npar; j++) {
    diff = (pot[j]-pot_direct[j])/pot_direct[j];
    diff = diff>0 ? diff : -diff;
    max_diff = diff>max_diff ? diff : max_diff;
  }

  printf("\n===== Max potential difference = %e =====\n",max_diff);
  printf("===== Total  energy  = %e   =====\n",
    eng);

  /*****************************************************************************/

tend = omp_get_wtime();
    	  printf("===== total time = %f =====\n\n",
    tend-tstart);

  return 0;
}
```
<table>
  <tr>
<td align="right"><br /><sub>  
    <img src="https://user-images.githubusercontent.com/80456274/152738073-50d81304-6662-4f5e-ae41-a44374e31789.png" alt="Logo" width="300" height="250">   
</sub><br /></td></tr>   
     
<td align="left"><br /><sub>  
    <img src="https://user-images.githubusercontent.com/80456274/152738073-50d81304-6662-4f5e-ae41-a44374e31789.png" alt="Logo" width="300" height="250">   
</sub><br /></td></tr>   
</table>

# Part-III



-------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Our Team     : [AIT EL KADI Ilyas](https://github.com/IlyasKadi) - [AZIZ Oussama](https://github.com/ATAMAN0)  
 
   Project Link : [The 2048 Game](https://github.com/IlyasKadi/poisson-boltzmann)   
 
  > Encadré par  : [Mr.ABBAD-Zakariae](https://github.com/IlyasKadi/poisson-boltzmann)  
                                                                                             
<p align="right">(<a href="#top">back to top</a>)</p>
