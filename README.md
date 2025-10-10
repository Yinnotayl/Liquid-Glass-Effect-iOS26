# Liquid Glass Effect iOS26
A HTML example of the new Liquid Glass effect brought by iOS 26

# Liquid Glass Distortion Calculation

## Introduction
Liquid Glass is part of the iOS 26 update developed by Apple, and it introduces many features, with the main theme being glass-like UI elements that reflect, refract and distort light like how glass distorts light in real life.

This project is to replicate this effect, and can be used in other non-iOS applications.

## Strategy
One key UI element of Liquid Glass is the capsule like buttons, that are translucent, and refracts light at the edges.

In real life, this capsule distortion is because of light bending when going through different mediums with different refractive indexes. In short, the further the distance from the center of the curved edges, the more inward the element refracts light.

We can mimic this efficiently by calculating pixel colours on the UI element itself. If you look closely at the UI elements in regular iOS apps, you can see that the distortion is not smooth and clean, but instead tiny visible pixels that are obviously separated, showing that Apple also used a similar ray tracing algorithm to create this wonderful effect so seamlessly at first glance while being resource efficient in terms of processing power.

## Calculating pixel distance

### Formulas & Theorems Required
Snell's law: \
$n_1 \sin \theta_1 = n_2 \sin \theta_2$

Do take note that trigonometric functions used here are in degrees, not in radians nor gradians.

### Inputs

$d$: Perpendicular distance from element edge to ray incident point \
$r$: Radius of edge \
$d_6$: Perpendicular distance from element bottom to ground \
$n_1$: Refractive index of surroundings (air = 1.0) \
$n_2$: Refractive index of element material (glass = 1.5)

### Outputs
$d_{final}$: Perpendicular distance from ray incident point on the ground to element edge


### Procedure
Angle between centre to incident point and horizontal radius \
$\theta_1=\arccos(\frac{r-d}{r})$

Angle between tangent line and extension of vertical radius\
$\theta_2=180^\circ-(90^\circ+(90^\circ-\theta_1))=180^\circ-(90^\circ+(90^\circ - \arccos(\frac{r-d}{r})))=\theta_1$

Angle of incidence of light ray \
$\angle i=90^\circ-\theta_1=90^\circ-\arccos(\frac{r-d}{r})=\arcsin(\frac{r-d}{r})$

Angle of refraction of light ray \
$\angle r=\arcsin(\frac{n_1\sin(\arcsin(\frac{r-d}{r}))}{n_2})=\arcsin(\frac{n_1(r-d)}{n_2})$

Angle between refracted light ray and perpendicular height from horizontal radius \
$\theta_3=90^\circ-\theta_1-\angle r=90^\circ-\arccos(\frac{r-d}{r})-\arcsin(\frac{n_1(r-d)}{n_2})$

Distance between perpendicular height's base from horizontal radius to point where the refracted ray crosses the horizontal radius \
$d_2=r\sin(\arcsin(\frac{r-d}{r}))\cdot \tan\theta_3=(r-d)\tan(90^\circ-\arccos(\frac{r-d}{r})-\arcsin(\frac{n_1(r-d)}{n_2}))$

Remaining distance of the horizontal radius \
$d_3 = r-d-d_2=(r-d)-(r-d)\tan(90^\circ-\arccos(\frac{r-d}{r})-\arcsin(\frac{n_1(r-d)}{n_2}))=(r-d)(1-\tan(90^\circ-\arccos(\frac{r-d}{r})-\arcsin(\frac{n_1(r-d)}{n_2})))$

Angle between refracted ray and horizontal radius \
$\theta_5=90^\circ-\theta_3=90^\circ-(90^\circ-\theta_1-\angle r)=90^\circ-90^\circ+\theta_1+\angle r=\arccos(\frac{r-d}{r})+\arcsin(\frac{n_1(r-d)}{n_2})$

Perpendicular distance from incident point of refracted ray on bottom arc of element to horizontal radius \
$d_4=(d_3\cos(\theta_5)+\sqrt{r^2 - (d_3)^2\cdot\sin^2(\theta_5)})\sin\theta_5$

Perpendicular distance from incident point of refracted ray on bottom arc of element to vertical radius \
$d_5=\left| -d_3 + (d_3 \cos\theta_5 + \sqrt{r^2-(d_3)^2\sin\theta_5})\cos\theta_5 \right|$

Angle between incident point of refracted ray on bottom arc of element to centre and horizontal radius \
$\theta_4=\arctan(\frac{d_4}{d_5})$

Second angle of incidence of refracted ray \
$\angle i_2 = 180^\circ - \theta_4 - \theta_5$

Second angle of refraction of refracted ray \
$\angle r_2 = \arcsin(\frac{n_1 \sin\angle i_2}{n_2})$

Perpendicular distance from ground to light ray's exit point \
$d_7 = d_6 +(r-d_4)$

Perpendicular distance from element's edge to incident point of refracted ray's on the ground \
$d_{final}=d_7\tan(\angle r_2 - \theta_6) + r - d_5$

With these calculations, you can find the appropriate pixel colour to shade onto your UI element based on it's position to mimic real refraction.
