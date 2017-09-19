---
title: "Mathematic Concept of Unscented Kalman Filter"
date: 2017-09-19T15:38:49+08:00
draft: true
---

<!-- s Here I put the resource of images -->

[CTRV_state_vector]: img/math_concept_for_ukf/CTRV_state_vector.png

<!-- align center (display block) : `$$ $$` -->
<!-- just inline: `$ $` -->

In UKF, we work with a moving object of interest under CTRV  (constant turn rate and velocity magnitude model) nonlinear motion model which assumes the object can move straight, but they can also move with a constant turn rate and a const velocity magnitude.

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

2. Change rate of state :  

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

3. Time difference :  `$ \Delta t = t_{k+1} - t_{k} $`

4. Process model (predicts the state at time step k+1) :  `$ x_{k+1} = f(x_{k}, \nu_{k}) $`  

  * Without consider noise:  

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
		      \frac{v{k}}{\psi_{k}} \left( sin(\psi_{k} + \dot{\psi_{k}} \Delta t) - sin(\psi_{k})  \right) \\
		      \frac{v{k}}{\psi_{k}} \left( -cos(\psi_{k} + \dot{\psi_{k}} \Delta t) + cos(\psi_{k})  \right) \\
		      0 \\
		      \dot{\psi_{k}} \Delta t \\
		      0
		    \end{matrix}
		  \right]
		  \end{align*}
		$`