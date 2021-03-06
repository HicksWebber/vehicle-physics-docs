# 3D models and environment

[TOC]

## Vehicles

A good vehicle 3D model mets these minimum standards:

- FBX file
- Forward direction is +Z
- Scale is 1 unit = 1 meter
- Rotations are 0, 0, 0 excepting the parts that have a logical orientation (i.e. steering wheel)
- Movable parts have their local origin placed at the rotation point (wheels, steering wheel...)
- Movable parts should have zero logical rotation at their default/rest position (i.e. doors)
- Wheels' origins should be at the geometric center of the wheel. Twin wheel groups (i.e. rear
	wheels at trucks) should have their origin at the center of both wheels.
- Wheel complements (brake calipers, suspension) should have the same origin as their corresponding
	wheels.
- All parts in hierarchy are named in plain _English_, so everyone can understand it (note that
	language-specific characters often cause missing meshes)

Importing the FBX alone should comply with these rules without having to create a prefab first.

A high quality vehicle 3D model also mets these conditions:

- Reasonable polygon density in all parts
- Good use of materials and textures.
- A good reference for essential materials is: car paint, glass, plastics, metals.
- Additional materials may be included as per specific details: mirrors, lights, chromed parts,
dashboard...
- Car paint has a properly unwrapped UV map so liveries can be painted at the texture.

#### Interior

Dashboard gauges and steering wheel must meet these requirements:

- The local origin is the center of rotation
- They rotate around the Z axis
- Default Z rotation is zero. Other axis may have some rotation applied

&fa-lightbulb-o:lg; Typically, the center of rotation of the steering wheel is the center of the
external circumference. Using the central part or logo as center is a common mistake.
{: .alert .alert-success }

#### Vehicle collider

Game-ready 3D vehicles usually come with a simplified shape of the vehicle (collision mesh). The
vehicle collider must be a **convex** collider (MeshCollider > Convex), or a set of convex
colliders.

!!! warning "&fa-warning:lg; At least one convex collider is mandatory in the vehicle"
	A vehicle without colliders will exhibit a very weird and unnatural behavior as soon as the
	simulation starts, unless the inertia tensor is explicitly configured (Rigidbody.inertiaTensor).

The collision mesh is used for collision detection and inertia calculations. The vehicle dynamics
are actually affected by the shape of the collider. Thus, a collider resembling the shape of its
vehicle will provide a better simulation.

![Convex vehicle collider](/img/user-guide/vpp-vehicle-collider.png){: .clickview .img-medium }

The collider of the vehicle can be:

- A collision mesh that comes with the 3D vehicle (best case).
- A box collider roughly resembling the vehicle's shape (not recommended, use for early prototyping
	only).
- A MeshCollider directly added to the 3D vehicle mesh. This may or	may not work depending on the
	actual complexity of the vehicle mesh. Most likely, exterior parts of the vehicle such as
	mirrors and antennas will have an adverse effect in the collisions.

!!! warning "&fa-warning:lg; VPWheelCollider components must be located _**inside**_ the vehicle's colliders"
	Ensure that the **top half** of each VPWheelCollider component is **enclosed within regular
	convex colliders** belonging to the vehicle's main rigidbody. Otherwise, strange behaviors and
	side effects may happen when the vehicle enters in contact with other objects.

	![Good (left) and bad (right) positions for the VPWheelCollider with respect to the vehicle collider](/img/user-guide/vpp-colliders-and-wheels.png){: .clickview .img-small }

#### Articulated vehicles

Multi-body vehicles such as trucks with trailers can be attached together. Each part is a 3D model
that can be setup as an independent vehicle with its own Vehicle Controller (i.e. trailers) or as a
a separated rigidbody with all wheels configured in the main Vehicle Controller (i.e. articulated bus).

Add a VPVehicleJoint component at the transform representing the _male_ plug:

- Other anchor: transform in the other part representing the _female_ connector.
- X, Y, Z motion: Locked
- Angular X, Y, Z motion: Free

Both bodies should be close enough to the connected position for preventing abrupt movements when
the VPVehicleJoint gets enabled.

Each body must have its collider according to the collider specifications above.

#### Driver's view

Configuring the head movement in the first person view provides a nice feeling of the inertia in
the driver's view. Add a VPHeadMotion component to a child gameobject of the vehicle:

- Longitudinal and Lateral movement can be either _tilt_ or _slide_.
- Vertical movement can be _tilt_ only.
- Spring Rate affects the maximum displacement (more spring -> less displacement).
- Damper Rate affects the bounciness of the movement (more damper -> less bounciness).

## Scenery

TO-DO:

Colliders, continuity, player safety.

Roads & tracks do & dont's

## Physic Materials

TO-DO:

Unity physic materials for vehicles and environment.

Different grip levels on different surfaces.

Settings to represent asphalt, grass, concrete walls, tire walls, etc

Learned from Box2D:
https://github.com/erincatto/Box2D/blob/master/Box2D/Box2D/Dynamics/Contacts/b2Contact.h

- The physically correct friction combination is **Multiply**:
Friction mixing law. The idea is to allow either fixture to drive the restitution to zero.
For example, anything slides on ice.

- The physically correct bounce combination is **Maximum**:
Restitution mixing law. The idea is allow for anything to bounce off an inelastic surface.
For example, a superball bounces on anything.

