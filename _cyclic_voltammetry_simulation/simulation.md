---
layout: page
categories: articles
title: "Cyclic Voltammetry App: Simulation details"
---

## UNDER CONSTRUCTION

In this post, I'll explain the cyclic voltammetry simulation I've created in
greater detail. You can find the entire script
[here](/cyclic_voltammetry_simulation/code.html).

~~~~matlab
%%%%%
% From Bard and Faulkner, 2nd edition, Appendix B
% Peter Attia
% Updated August 31, 2017
%%%%%
~~~~

Again, this simulation is well-described in Bard and Faulkner, Appendix B.
Highly recommended for all the details.
This walkthrough is designed to serve as a "practical" guide to performing these
simulations, as well as to make Bard and Faulkner's
occasionally dense writing style more accessible.

~~~~matlab
clear, clc, close all

%%% INDEPENDENT VARIABLES %%%
C      = 1.0;   % [=] mol/cm^3, initial concentration of O. Default = 1.0
D      = 1E-5;  % [=] cm^2/s, O & R diffusion coefficient. Default = 1E-5
etai   = +0.2;  % [=] V, initial overpotential (relative to redox potential). Default = +0.2
etaf   = -0.2;  % [=] V, final overpotential (relative to redox potential). Default = -0.2
v      = 1E-3;  % [=] V/s, sweep rate. Default = 1E-3
n      = 1.0;   % [=] number of electrons transfered. Default = 1
alpha  = 0.5;   % [=] dimensionless charge-transfer coefficient. Default = 0.5
k0     = 0.01;  % [=] cm/s, electrochemical rate constant. Default = 1E-2
k1     = 0;    % [=] 1/s, chemical rate constant. Default = 0
~~~~

These variables are the "adjustable" parameters in this simulation.
These parameters should be straightforward to someone interested in cyclic
voltammetry simulations. I'll note that $ k_0 $ is the electrochemical rate constant,
with units of cm/s, and $ k_1 $ is the chemical rate constant, with units of 1/s.
You can read more about these terms on the
[fundamentals](/cyclic_voltammetry_simulation/fundamentals.html) page.

~~~~matlab
%%% CONSTANTS %%%
F      = 96485;   % [=] C/mol, Faraday's constant
R      = 8.3145;  % [=] J/mol-K, ideal gas constant
T      = 298.15;  % [=] K, temperature. Default = 298.15
f      = F/(R*T); % [=] 1/V, normalized Faraday's constant at room temperature
~~~~

These variables are fundamental physical constants.
The [Faraday constant](https://en.wikipedia.org/wiki/Faraday_constant)
is often normalized by $ RT $.

In my work, I don't consider temperatures other than room temperature.
If your work involves variable temperature, you can move $ T $ from "constants"
to "independent variables".

~~~~matlab
%%% SIMULATION VARIABLES %%%
L      = 500;    % [=] number of iterations per t_k (pg 790). Default = 200
DM     = 0.45;   % [=] model diffusion coefficient (pg 788). Default = 0.45

%%% DERIVED CONSTANTS %%%
j      = ceil(4.2*L^0.5)+5;   % [=] number of boxes. If L~200, j=65
Deta   = (etai-etaf)/v;       % [=] s, time of one scan (pg 790)
tk     = 2*Deta;              % [=] s, characteristic exp. time (pg 790). In this case, total time of fwd and rev scans
Dt     = tk/L;                % [=] s, delta time (Eqn B.1.10, pg 790)
Dx     = sqrt(D*Dt/DM);       % [=] cm, delta x (Eqn B.1.13, pg 791)
ktk    = k1*tk;               % [=] dimensionless kinetic parameter (Eqn B.3.7, pg 797)
km     = ktk/L;               % [=] normalized dimensionless kinetic parameter (see bottom of pg 797)
Lambda = k0/(D*f*v)^0.5;      % [=] dimensionless reversibility parameter (Eqn 6.4.4, pg. 236-239)

if km>0.1
    warning(['k_c*t_k/l equals ' num2str(km) ...
        ', which exceeds the upper limit of 0.1 (see B&F, pg 797)'])
end

%%% START SIMULATION %%%
k = 0:L;
t = Dt * k;
X = (k+1)/sqrt(L*DM);
eta1 = etai - v*t; % negative scan
eta2 = etaf + v*t; % positive scan
eta = [eta1(eta1>etaf) eta2(eta2<=etai)]'; % eta includes both fwd and rev
Enorm = eta*f;
kf = ( k0*tk*exp(  -alpha *Enorm) )./( sqrt(DM*L)*Dx );
kb = ( k0*tk*exp((1-alpha)*Enorm) )./( sqrt(DM*L)*Dx );

O = C*ones(length(k)-1,j); % Initial concentrations of O
R = zeros(length(k)-1,j);  % Initial concentrations of R
Z = zeros(length(k)-1,1);  % Dimensionless current

% First dimensionless current
Z(1) = ( kf(1)*O(1,2) - kb(1)*R(1,2) ) ./ (1 + kf(1) + kb(1));

% k = time index. j = distance index
for i1 = 2:length(k)-1
    % Update surface concentrations
    Z(i1)   = ( kf(i1).*O(i1,2) - kb(i1).*R(i1,2) ) ./ (1 + kf(i1) + kb(i1));
    O(i1,1) = O(i1,2) - Z(i1) + km*R(i1-1,1);
    R(i1,1) = R(i1,2) + Z(i1) - km*R(i1-1,1);

    % Update bulk concentrations of O and R
    for i2 = 2:j-1
        O(i1,i2) = O(i1-1,i2) + DM*(O(i1-1,i2+1)+O(i1-1,i2-1)-2*O(i1-1,i2)) ...
            + km * R(i1-1,i2);

        R(i1,i2) = R(i1-1,i2) + DM*(R(i1-1,i2+1)+R(i1-1,i2-1)-2*R(i1-1,i2)) ...
            - km * R(i1-1,i2);
    end

    % Update current.
    % We flip the sign of current because the Bard and Faulkner current
    % convention is weird
    Z(i1)   = -( kf(i1).*O(i1,2) - kb(i1).*R(i1,2) ) ./ (1 + kf(i1) + kb(i1));
end

% Clip to be same size
eta = eta(1:length(Z));

plot(eta,Z)
xlabel('Overpotential (V)'), ylabel('Dimensionless current')
~~~~