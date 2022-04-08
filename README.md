# Memristor
SPICE Memristor model of NaMLab gGmbH Al2O3/Nb2O5 bi-layer device


* Namlab analog core memristor
* Author: Richard Schroedter, April 2022
* Connections:
*   TE: Top electrode
*   BE: Bottom electrode
*   SV: State variable for plot
.SUBCKT MEM_NAMLAB_CORE TE BE SV

.params Ar=4.7447e-8 As=1.1253e-8 Br=2.6831 Bs=9.3348
+ c1=2.9457e-4 c2=57414 c3=11103 wc=1000 xon=0.1 xoff=0.284

* Function G(x,Vm) - Core Memductance
.func G(x,Vm)=Ar*x*exp(Br*sgn(Vm)*sqrt(abs(Vm)/x))+
+ As*x*exp(-Bs*sgn(Vm)*sqrt(abs(Vm)))

* Function w(x) - Window function 
.func win_off(x) = -exp((x-xoff)*wc)
.func win_on(x)  = -exp(-(x-xon)*wc)

* Function F(Vm,x) - State equation
.func F(Vm,x)=c1*(exp(c2*x*Vm*G(x,Vm)+win_off(x))-
+ exp(-c3*x*Vm*G(x,Vm)+win_on(x)))

* Circuit to determine state variable dx/dt = F(Vm,x)
Cx SV 0 {1}
.ic V(SV) = {x0}
Gx 0 SV value = {F(V(TE,BE),V(SV,0))}

* Current source for memristor IV response
Gmem TE BE value={V(TE,BE)*G(V(SV,0),V(TE,BE))}

.ENDS MEM_NAMLAB_CORE



![SPICE](https://user-images.githubusercontent.com/103260242/162437716-61172ee0-8e74-4131-9095-1472ce789340.jpg)
