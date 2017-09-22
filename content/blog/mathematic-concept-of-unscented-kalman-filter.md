+++
title = "Mathematic Concept of Unscented Kalman Filter with CTRV model"
date = 2017-09-19T15:38:49+08:00
draft = false
+++

<!-- s Here I put the resource of images -->

[CTRV_state_vector]: img/math_concept_for_ukf/CTRV_state_vector.png
[Assume_yaw_rate_low]: img/math_concept_for_ukf/assume_yaw_rate_low.png
[process_noise_vector]: img/math_concept_for_ukf/process_noise_vector.png
[process_noise_covariance]: img/math_concept_for_ukf/process_noise_covariance.png
[generate_simga_points]: img/math_concept_for_ukf/generate_simga_points.png
[predict_simga_points]: img/math_concept_for_ukf/predict_simga_points.png
[calculate_mean_and_covariance_from_predict_sigma_points]: img/math_concept_for_ukf/calculate_mean_and_covariance_from_predict_sigma_points.png
[measurement_model]: img/math_concept_for_ukf/measurement_model.png
[measurement_sigma_points]: img/math_concept_for_ukf/measurement_sigma_points.png
[calculate_mean_and_covariance_from_measuremnt_sigma_points]: img/math_concept_for_ukf/calculate_mean_and_covariance_from_measuremnt_sigma_points.png

[Self_Driving_Nanodegree_url]: https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013

<!-- align center (display block) : `$$ $$` -->
<!-- just inline: `$ $` -->

### CTRV Model (constant turn rate and velocity magnitude model) 
---
Here we work with a moving object of interest under CTRV nonlinear motion model which assumes the object can move straight, but they can also move with a constant turn rate and a const velocity magnitude.

1. The State Vector of CTRV model:  

	`$ x = [ 
	  \begin{matrix} 
	    p_{x} & p_{y} & v & \psi & \dot{\psi} 
	  \end{matrix} ]^T 
	$`

  * $v$ : speed which describes the magnitude
  * $\psi$ : yaw angle which describes the orientation
  * $\dot{\psi}$ : yaw rate  


	![CTRV_state_vector][CTRV_state_vector]

	&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.
  

2. Change rate of state:  

	`$
	  \begin{align}
	    \dot{x}
		  &= \begin{matrix} 
	         [ \dot{p_{x}} & \dot{p_{y}} & \dot{v} & \dot{\psi} & \ddot{\psi} ]^T 
			   \end{matrix} \\
	    &= \begin{matrix} 
	  	     [ v \cdot cos(\psi) & v \cdot sin(\psi) & 0 & \dot{\psi} & 0 ]^T
	  	   \end{matrix}
	  \end{align}
	$`

3. Time difference:&emsp;`$ \Delta t = t_{k+1} - t_{k} $`

4. Process model (predicts the state at time step k+1):&emsp;  

	`$ 
			x_{k+1} 
			\begin{align*}
			&= f(x_{k}, \nu_{k}) 
			&= x_{k} + 
		  \left[ 
		    \begin{matrix} 
		      \frac{v_{k}}{\dot{\psi_{k}}} \left( sin(\psi_{k} + \dot{\psi_{k}} \Delta t) - sin(\psi_{k})  \right) \\
		      \frac{v_{k}}{\dot{\psi_{k}}} \left( -cos(\psi_{k} + \dot{\psi_{k}} \Delta t) + cos(\psi_{k})  \right) \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right] +
			\left[ 
	    \begin{matrix} 
	      \frac{1}{2} (\Delta t)^2 cos(\psi_{k}) \cdot \nu_{a,k} \\
	      \frac{1}{2} (\Delta t)^2 sin(\psi_{k}) \cdot \nu_{a,k} \\
	      \Delta t \cdot \nu_{a,k} \\
	      \frac{1}{2} (\Delta t)^2 \cdot \nu_{\ddot{\psi}, k} \\
	      \Delta t \cdot \nu_{\ddot{\psi}, k}
	    \end{matrix}
	  	\right]
			\end{align*}
	 $`  

	**Note: We should be careful when `${\dot{\psi_{k}}}$ = 0`, to avoid divison by 0**  
	(This is at the situation when the yaw angle is not change, the car is going straight)

	**if `${\dot{\psi_{k}}}$` is zero  
	`$
		x_{k+1} 
			\begin{align*}
			&= x_{k} + 
		  \left[ 
		    \begin{matrix} 
		      {v_{k}} cos(\psi_{k}) \Delta t \\
		      {v_{k}} sin(\psi_{k}) \Delta t \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right] +
			\left[ 
	    \begin{matrix} 
	      \frac{1}{2} (\Delta t)^2 cos(\psi_{k}) \cdot \nu_{a,k} \\
	      \frac{1}{2} (\Delta t)^2 sin(\psi_{k}) \cdot \nu_{a,k} \\
	      \Delta t \cdot \nu_{a,k} \\
	      \frac{1}{2} (\Delta t)^2 \cdot \nu_{\ddot{\psi}, k} \\
	      \Delta t \cdot \nu_{\ddot{\psi}, k}
	    \end{matrix}
	  	\right]
			\end{align*}
	$`**
  * consider only deterministic part:  

		`$
		 \begin{align*}
		 x_{k+1} 
		  &= f(x_{k}) \\
		  &= x_{k} + \int^{t_{k+1}}_{t_{k}} \left[ \begin{matrix} v(t) \cdot cos(\psi(t)) & v(t) \cdot sin(\psi(t)) & 0 & \dot{\psi} & 0 \end{matrix} \right]^T dt \\
		  &= x_{k} + 
		  \left[ 
		    \begin{matrix} 
		      \int^{t_{k+1}}_{t_{k}} v(t) \cdot cos(\psi(t)) dt   \\
		      \int^{t_{k+1}}_{t_{k}} v(t) \cdot  sin(\psi(t)) dt   \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right]
		  = x_{k} + 
		  \left[ 
		    \begin{matrix} 
		      v_{k} \int^{t_{k+1}}_{t_{k}} cos( \psi_{k} + \dot{\psi_{k}} \cdot (t - t_{k})  ) dt   \\
		      v_{k} \int^{t_{k+1}}_{t_{k}} sin( \psi_{k} + \dot{\psi_{k}} \cdot (t - t_{k}) ) dt   \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right] \\
		  &= x_{k} + 
		  \left[ 
		    \begin{matrix} 
		      \frac{v{k}}{\dot{\psi_{k}}} \left( sin(\psi_{k} + \dot{\psi_{k}} \Delta t) - sin(\psi_{k})  \right) \\
		      \frac{v{k}}{\dot{\psi_{k}}} \left( -cos(\psi_{k} + \dot{\psi_{k}} \Delta t) + cos(\psi_{k})  \right) \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right]
		  \end{align*}
		$`

	* stochastic part (noise vector):&emsp;`$ \nu_{k} = \begin{matrix} [\nu_{a,k} & \nu_{\ddot{\psi}, k}]^T \end{matrix} $`
		* longitudinal acceleration noise:&emsp;`$ \nu_{a,k} \sim \mathcal{N}(0, \sigma^2_{a}) $`
		* &emsp;&emsp;&emsp;&nbsp; yaw acceleration noise:&emsp;`$ \nu_{\ddot{\psi}, k} \sim \mathcal{N}(0, \sigma^2_{\ddot{\psi}}) $`
		
		* Assume that both longitudinal and yaw acceleration noise are constant between k and k+1 step:  

			(also we assume that the car wre driving prefectly straight, so we could calculate x accelertation and y acceletration accordingly.)  
			**(this approximation will be okay as long as the yaw rate is not too high!)**

			`$
			\begin{align*}
			f(\nu_{k})
				&= 
				\left[ 
		    \begin{matrix} 
		      \frac{1}{2} (\Delta t)^2 cos(\psi_{k}) \cdot \nu_{a,k} \\
		      \frac{1}{2} (\Delta t)^2 sin(\psi_{k}) \cdot \nu_{a,k} \\
		      \Delta t \cdot \nu_{a,k} \\
		      \frac{1}{2} (\Delta t)^2 \cdot \nu_{\ddot{\psi}, k} \\
		      \Delta t \cdot \nu_{\ddot{\psi}, k}
		    \end{matrix}
		  	\right]
			\end{align*}
			$`  

			![Assume_yaw_rate_low][Assume_yaw_rate_low]  
			&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.


### UKF (Unscented Kalman Filter)
---
What makes UKF and EKF (Extended Kalman Filter) different is the method they uses to tackle with non-linear motion model and measurement model.  

* EKF uses the Jacobian matrix to linearize to non-linear functions.
* UKF takes representative points of the whole distribution called **simga points** from a Gaussian distribution, and put them into the non-linear function which is called **unscented transformation**. And it will come out the **correspond simga points** in the predicted or measurement state space. Then we can calculate the mean vector and covariance matrix from these correspond simga points to get the Gaussian distribution of the predicted or measurement state space.

We can split the unscented prediction into three parts.
1. generate simga points.
2. insert them into the process function or measurement function to do unscented transformation.
3. calculate the mean and covariance from the predicted or measurement sigma points.

### Prediction Stage

#### Generate Simga Points
With CTRV model, we have state dimension `$ n_{x} = 5 $`. However, we should also consider the process noise vector, which has two-dimension, because it also has a non-linear effect. `$ n_{aug} = 5+2 = 7 $`. We will choose `$ 2 n_{aug} + 1 $` sigma points. 

![process_noise_vector][process_noise_vector]  
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

![process_noise_covariance][process_noise_covariance]  
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

![generate_simga_points][generate_simga_points]
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

#### Prediction Step
We simply insert every sigma point into the process model of CTRV.

![predict_simga_points][predict_simga_points]
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

#### Calculate Mean and Covraiance from the Predicted Sigma Points
When generate simag points, we use lambda to get spreading value from mean.
Now we consider to do the inverse calculation. (There are several ways to calculate weights, we just stick to this one.)

* Weights:

	`$ \omega_{i} = \frac{\lambda}{ \lambda+n_{aug} } $`&emsp;,&emsp; `$i = 0$`  
	`$ \omega_{i} = \frac{1}{ 2(\lambda+n_{aug}) } $`&emsp;,&emsp; `$i = 1 ... 2*n_{aug}$`

![calculate_mean_and_covariance_from_predict_sigma_points][calculate_mean_and_covariance_from_predict_sigma_points]
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

**Note: There is an error in picture above, the predicted mean formula should be&emsp;`$ x_{k+1|k} = \Sigma_{i=0}^{2n_{aug}} \omega_{i} \cdot \chi_{k+1|k,i} $`**  

### Measurement Stage -- Take Radar sensor data for instance
Like what we do in Prediction Stage, measurement model is also non-linear that we need to put several sigma points into measurement function. But here we could have two shortcut.  

* First, we could directly put the predicted sigma points generated from Prediction Step into the measurement model.  
* Second, we don't have to augment the predicted sigma points with measurement noise vector, because it has no non-linear effect in our measurement model.

![measurement_model][measurement_model]  

&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

![measurement_sigma_points][measurement_sigma_points]  

&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.

![calculate_mean_and_covariance_from_measuremnt_sigma_points][calculate_mean_and_covariance_from_measuremnt_sigma_points]  
&emsp;&emsp;&emsp;&emsp;&emsp;the picture above is from Udacity [Self-Driving Nanodegree][Self_Driving_Nanodegree_url] project under CC BY-NC-ND 4.0.


**Note: There is also an error in predicted measurement mean formula, the predicted mean formula should be&emsp;`$ z_{k+1|k} = \Sigma_{i=0}^{2n_{aug}} \omega_{i} \cdot Z_{k+1|k,i} $`**  