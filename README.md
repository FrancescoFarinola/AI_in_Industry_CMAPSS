# AI in Industry - CMAPSS
Project work for AI in Industry course on CMAPSS dataset.

The aim of this project is to discover why, when obtaining RUL predictions, we get 'flat' predictions at the beginning of each experiment.

During the course, we investigated one potential issue which is related to a strongly unbalanced dataset. In these kind of situation, SGD optimization may have convergence issues since the gradient will push strongly in the direction of the overrepresented class. A common practice to address this issue is using class weights. Typically, we use weights inversely proportional to the counts so as to counter-balance the effect.

Results obtained were worse since this method increases the chances of an undesirable early stop.

Another way to mitigate this is to cut the sequences of experiments so as all experiments have the same length. This is exactly what this project tries to figure out.

By cutting all the sequences with the minimum length, we still found some 'flat' predictions in previous short experiments. So, we decided to cut even more the experiments to prove that a potential fault could have risen at half of the experiment. By looking at the obtained results, we could conclude that the initial 'plateau' in the base model predictions is due to the absence of faults.

To prove this, we looked better at how data of sensors in generated from the paper "*Damage Propagation Modeling for Aircraft Engine Run-to-Failure Simulation*" by Abhinav Saxena, Kai Goebel, Don Simon, Neil Eklund.


What comes out is that there is likely no fault that arises suddently, but the life of an engine decreases due to the progressive usage of it based on different amount and rate of damage accumulation, like the fault increases exponentially for all the duration of the experiment.   

We can prove this by looking at the process of data generation:


1.   Choose initial deterioration $(f_0, e_0)$ where $f_0 \in [0.99,1]$ and $e_0 \in [0.99,1]$. 
This initial wear can occur due to manufacturing and assembly variations and is modelled in flow and efficiencies of the various modules. Those values are taken from a random distribution, such that the maximum initial deterioration is bounded within 1% degradation of the healthy condition.

2.   Impose an exponential rate of change for flow and efficiency loss for each dataset, denoting an otherwise unspecified fault with increasingly worsening effect as: 


\begin{equation}
  h(t) =  1 - exp\{at^b\}
\end{equation}


This results in the overall health index:

\begin{equation}
H(t)=g(e(t),f(t))
\end{equation} 

varying as a function of time where:

\begin{equation}
e(t)=1-d_e-exp\{a_e(t)t^{b_e(t)}\}
\end{equation}
\begin{equation}
f(t)=1-d_f-exp\{a_f(t)t^{b_f(t)}\}
\end{equation}

and the function g is the minimum of all operative margins considered (Fan, HPC, HPT and EGT modules).
This randomly chosen direction and evolution of faults is constrained by:

\begin{equation}
f,e \leq 1%,
\end{equation}
\begin{equation}
a_k \in [0.001, 0.003]
\end{equation}
\begin{equation}
b_k \in [1.4, 1.6], k=1,2.
\end{equation}

3. Stop when health H=0 (failure criterion).

4. Superimpose measurement noise to the output data.

Also, in the data we are using, it is made explicit that degradation was intentionally made only on the HPC module.

The proof is found in the step 2, where we see that the rate of damage increases exponentially based on the two parameters $a_k$ and $b_k$ where $a_k$ is the amount of wear of a particular module k at a certain time t and $b_k$ is the rate of time at which exponential degradation happens (a kind of speed of degradation regularizer).

In conclusion, the health index almost reflects the generic behaviour of an exponential function. Thus, this is why in our predictions we have an initial 'plateau' and suddently it exponentially decreases.
