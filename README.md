<div id="top"></div>


<!-- PROJECT LOGO -->
<br />
<div align="center">
    <img src="https://user-images.githubusercontent.com/80456274/171751416-702b48cb-ef20-4f52-8aac-e7d1f3705a98.png" alt="Logo" width="550" height="380">   
    <h2 align="center">FMM (fast multipole methode) </h2>
    <h3 align="center">Mini Projet</h3>
</div>

<!-- TABLE OF CONTENTS -->

  <summary>Table of Contents</summary>

  <ol>
    <li>
      <a href="#Part-I">Part I : About the project</a>
         <ul>
              <li><a href="#"></a></li>
           </ul>
    </li>
        <li>
            <a href="#Part-II">Part II : Seq-code vs paralle-code</a>
            <ul> 
              <li><a href="#"></a></li>
            </ul>
           </li> 
        <li><a href="#Part-III">Part III :Parallelism per thread</a></li>
   </ol>






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
    <td align="center"><br /><sub>
      Seq_exmpl
   </sub><br /></td>

   <td align="center"><br /><sub>  
       parralel_version
   </sub><br /></td>
</tr>
    

<tr>
    <td align="center"><br /><sub>
      <img width="436" alt="seqq_ex" src="https://user-images.githubusercontent.com/85369625/171747792-3c0e7bb8-969e-4af0-9f8c-f222df073e88.PNG">
   </sub><br /></td>

   <td align="center"><br /><sub>  
            <img width="479" alt="parr_" src="https://user-images.githubusercontent.com/85369625/171747697-101cab19-1184-46cc-a832-0eab0f589395.PNG">
   </sub><br /></td>
</tr>
</table> 
    


# Part-III
## parallelism per thread
After parallelization, we change the value of num_thread to examine how the optimization performs with different thread counts. [2,4,8,16] the value

> We add two variable to be able to control the values of CHUNKS (iterations per thread) and NUM_THREADS (num_thread)
```C
#define NUM_THREADS 16
#define CHUNK 20
```

<table>
<tr>
    <td align="center"><br /><sub>
        #define NUM_THREADS 2
   </sub><br/></td>

   <td align="center"><br /><sub>  
     #define NUM_THREADS 4
   </sub><br /></td>
</tr>
    


<tr>
    <td align="center"><br /><sub>
<img width="304" alt="2_threads" src="https://user-images.githubusercontent.com/85369625/171751085-e05c1f5f-0738-41bd-8f3b-8db9b478a50f.PNG">
   </sub><br /></td>

   <td align="center"><br /><sub>  
<img width="306" alt="4_threads" src="https://user-images.githubusercontent.com/85369625/171751112-ba055adc-91dc-4ba6-a006-b4862c172e7d.PNG">
   </sub><br /></td>
</tr>

<tr>
    <td align="center"><br /><sub>
      #define NUM_THREADS 8
   </sub><br /></td>

   <td align="center"><br /><sub>  
       #define NUM_THREADS 16
   </sub><br /></td>
</tr>
    

<tr>
    <td align="center"><br /><sub>
<img width="306" alt="8_threads" src="https://user-images.githubusercontent.com/85369625/171751127-c87a09d5-0521-4705-bec4-d2975dbbe2a7.PNG">
   </sub><br /></td>

   <td align="center"><br /><sub>  
<img width="325" alt="16_thread" src="https://user-images.githubusercontent.com/85369625/171751134-0e9e2585-02df-4bf5-939e-b40769bbf085.PNG">
   </sub><br /></td>
</tr>
</table> 

> So, ultimately, we go from 19.19 seconds to 1.006 seconds, which is a 94.8 percent improvement.





-------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Our Team     : [AIT EL KADI Ilyas](https://github.com/IlyasKadi) - [AZIZ Oussama](https://github.com/ATAMAN0)  
 
   Project Link : [FMM_parall](https://github.com/ATAMAN0/FMM_parall)   
 
  > Encadré par  : [Mr.ABBAD-Zakariae](https://github.com/ATAMAN0/FMM_parall)  
                                                                                             
<p align="right">(<a href="#top">back to top</a>)</p>
