# PDDL 

This section describes pddl in detail and discusses the format for writing a pair of pddl file - Domain file and problem file. I then discuss Syntax of these files and go over some examples for better understanding.

**Table of Contents**
* [How to write a Domain Definition file]()
* [How to write a Problem Definition file]()
* [Syntax]()
* [Examples]()
* [Further Reading]()

## Format

Planning tasks specified in PDDL are separated into two files:

* A domain file for predicates and actions
* A problem file for objects, initial state and goal specification

**Note: Although not required by the PDDL standard, most planners require that the two parts are in the separate files.**

**PDDL (the "Planning Domain Definition Language")** is a recent attempt to standardise planning domain and problem description languages. It was developed mainly to make the International Planning Competition (IPC) series possible. The planning competition compares the performance of planning systems on sets of benchmark problems, so a common language for specifying problems must be used.

PDDL contains STRIPS(Stanford Research Institute Problem Solver), ADL(action description language) and much, much more. Most planners, however, do not support full PDDL. The majority support only the STRIPS subset, or some small extension of it. PDDL has evolved over time, with minor and major revisions published at each planning competition: generally recognised versions are 

* 1.0 (used in IPC 1998)
* 2.0 (used in IPC 2000) 
* 2.1 (used in IPC 2002) 
* 2.2 (used in IPC 2004) 
* 3.0 (used in IPC 2006) 
* 3.1 (used in IPC 2008)

NOTE: As mentioned above, most planners do not support all elements of (any version of) PDDL. Moreover, many planners have their own little "excentricities", meaning that they may interpret certain PDDL constructs incorrectly, or require minor variations in syntax that is at odds with the official language specification. Some examples:

* Some planners have an implicit constraint that all arguments to an action are distinct.
* Some planners require action preconditions and/or effects to be written as conjunctions (i.e., `as (and ...)`) even when the precondition/effect contains only one atomic condition, or even no condition at all.
* Most planners actually ignore the `:requirements` part of the domain definition. But, in spite of this, some planners may fail to parse a domain definition if this part is missing or if it contains a keyword they do not recognise.

A good rule of thumb while writting PDDL is to always use simple constructs to express the problem.  And, always read the documentation of the planner being used. 

A comprehensive list of planner can be found at the ipc (internation planning competition) [webpage](http://icaps-conference.org/index.php/Main/Competitions)

### Domain Definition

The format of a (simple) domain definition is:

```
(define (domain DOMAIN NAME)
  (:requirements [:strips] [:equality] [:typing] [:adl])
  (:predicates (PREDICATE_1_NAME ?A1 ?A2 … ?AN)
          (PREDICATE_2_NAME ?A1 ?A2 … ?AN)
           … )
(:action ACTION_1_NAME
	[:parameters (?P1 ?P2 … ?PN)]
	[:precondition PRECOND_FORMULA]
	[:effect EFFECT_FORMULA]
)
(:action ACTION_2_NAME
...)
...)
```

**The elements in [ ]’s are optional. This is a standard in [formal grammar](https://en.wikipedia.org/wiki/Formal_grammar).**

Names (domain, predicate, action, etc.) are usually made up of alphanumeric characters, hyphens (`-`) and underscores (`_`), but there may be some planners that allow less.

Parameters of predicates and actions are distinguished by their beginning with a question mark (`?`).

**The parameters used in predicate declarations (the `:predicates` part) have no other function than to specify the number of arguments that the predicate should have**, i.e. the parameter names do not matter (as long as they are distinct). Predicates can have zero parameters (but in this case, the predicate name still has to be written within parentheses).

### Problem Definition

The problem definition contains the objects present in the problem instance, the initial state description and the goal 

The format of a (simple) problem definition is: 

```
(define (problem PROBLEM_NAME)
  (:domain DOMAIN_NAME)
  (:objects OBJ_1 OBJ_2 … OBJ_N)
  (:init ATOM_1 ATOM_2 … ATOM_N)
  (:goal CONDITION_FORMULA)
)
```

Note: Some planners may require that the :requirements specification appears also in the problem definition (usually either directly before or directly after the `:domain` specification).

The initial state description (the `:init` section) is simply a list of all the ground atoms that are true in the initial state. All other atoms are by definition false. The goal description is a formula of the same form as an action precondition. All predicates used in the initial state and goal description should naturally be declared in the corresponding domain.

In difference to action preconditions, however, the initial state and goal descriptions should be ground, meaning that all predicate arguments should be object or constant names rather than parameters. (An exception is quantified goals in ADL domains, where of course the quantified variables may be used within the scope of the quantifier). 

Note, however, that even some planners that claim to support ADL do not support quantifiers in goals.

## Syntax

A simple construct of PDDL consists of two files :

* A domain file look like this

The domain definition contains the domain predicates and operators (called action in PDDL). It may also contain types, constants, static facts and many other things but these might not be supported by all the planners that exist out there. 

```
(define (domain <domain name>)
	<PDDL code for requirements>
	<PDDL code for types>
<PDDL code for predicates>
<PDDL code for first action>
[...]
<PDDL code for last action>
)
```

Here, `<domain name>` is a string that identifies the planning domain. 

* A problem file looks like this:

```
(define (problem <problem name>)
	(: domain <domain name>)
	<PDDL code for objects>
	<PDDL code for initial states>
<PDDL code for goal specification>
)
```

`<problem name>` is the string that identifies the planning task e.g gripper with 4 balls to move

### Comments

A comment in a PDDL file starts with a semicolon(`;`) 

### Requirements

Because PDDL is a very general language and most planners support only a subset, domains may declare requirements. The most commonly used requirements are:

1. `strips`: The most basic subset of PDDL, consisting of STRIPS only. The actions will only use positive preconditioning (predicates that must be true in the current state to trigger actions) and deterministic effects (effects that necessarily follow action triggering). Nothing else is allowed. 

2. `equality`: This requirement means that the domain uses the predicate =, interpreted as equality.

3. `typing`: This requirement means that the domain uses types (see Typing below).

4. `adl`: Means that the domain uses some or all of ADL (i.e. disjunctions and quantifiers in preconditions and goals, quantified and conditional effects).

We write it as (`:requirements` `:strips` `:typing`). Refer to the [Format’s]() domain definition section.

### Typing

Types are like “variable types” in coding. These can be inherited as well, as can be seen in the [arch example]() in the examples section. If types are to be used in a domain, the domain should declare the requirement `:typing` as in [Format’s]() domain definition section.

Type names have to be declared before they are used (i.e usually before `:predicates` declaration). This is done with the declaration

```
(:type NAME_1  NAME_2  …. NAME_N )
```

To declare the type of a parameter of a predicate or action, we can write `?x - TYPE_OF_X`. A list of parameters of the same.  A list of parameters of the same type can be abbreviated to `?X ?Y ?Z - TYPE_OF_XYZ`. The syntax is the same for declaring types of objects in the problem definition. 

Note: The hyphen between the parameter and type name has to be “free-standing” i.e surrounded by whitespace. 

### Functions

If we are going to use functions then we need to declare `:fluents` (according to PDDL 3.1) in the requirements section in the domain file. Unlike types, functions can be declared after the `:predicates` section. This is done with the declaration 
```
(: functions (function name - function type ?function arguments))
```

Here the function type is like the return type. Functional argument is optional though!

E.g 
```
(:functions (at - location ?o - object)
	    (robot-at - generalized_loc))
```

Here `at` is the name of a function and the function returns a variable of type of location (a type defined before) and takes in a parameter of type of object. Note `?` denotes the start of a variable and `o` represents the object name while after the `-` is the variable type. The second function `robot-at` does not take any argument and returns a variable of type `generalized_loc`. 

### Predicates

All parts of an action definition except the name are, according to the PDDL specification, optional (although, of course, an action without effects is pretty useless). However, for an action that has no preconditions some planners may require an "empty" precondition, of the form `:precondition ()` or `:precondition (and)`, and some planners may also require an empty `:parameter` list for actions without parameters)

**Note: Some planners require that the arguments to an action are all different, i.e. the same object may not instantiate two parameters. This may cause some difficulties (e.g. problems becoming unsolvable) if one is not aware of it.**

#### Preconditioning Formulas

In a STRIPS domain, a precondition formula may be:

	* An atomic formula:
	`(PREDICATE_NAME ARG1 ... ARG_N)`

The predicate arguments must be parameters of the action (or constants declared in the domain, if the domain has constants).

	* A conjunction of atomic formulas:
	`(and ATOM1 ... ATOM_N)`

If the domain uses `:equality`, an atomic formula may also be of the form `(= ARG1 ARG2)`. Many planners that support equality also allow negated equality, which is written `(not (= ARG1 ARG2))`, even if they do not allow negation in any other part of the definition.

In an ADL domain, a precondition may in addition be:

	* A general negation, conjunction or disjunction:
	```
	(not CONDITION_FORMULA)
	(and CONDITION_FORMULA1 ... CONDITION_FORMULA_N)
	(or CONDITION_FORMULA1 ... CONDITION_FORMULA_N)
	```

	* A (universally) quantified formula:
	```
	(forall (?V1 ?V2 ...) CONDITION_FORMULA)
	(exists (?V1 ?V2 ...) CONDITION_FORMULA)
	```

Here the `forall` operator can interpreted as `(forall (?v1 …. ?vN) <effect>)` and conditions statements as `(when <condition> <effect>)`. 


#### Effect Formulas

In PDDL, the effects of an action are not explicitly divided into `adds` and `deletes`. Instead, negative effects (`deletes`) are denoted by negation.

In a STRIPS domain, an effect formula may consist of:

	* `(PREDICATE_NAME ARG1 ... ARG_N)`
	The predicate arguments must be parameters of the action (or constants declared in the domain, if the domain has constants).

	* A deleted atom:
	`(not (PREDICATE_NAME ARG1 ... ARG_N))`

	* A conjunction of atomic effects:
	`(and ATOM1 ... ATOM_N)`

In an ADL domain, an effect formula may in addition contain:

	* A conditional effect:
	`(when CONDITION_FORMULA EFFECT_FORMULA)`

	The interpretation is that the specified effect takes place only if the specified condition formula is true in the state where the action is executed. Conditional effects are usually placed within quantifiers.

	* A universally quantified formula:
	`(forall (?V1 ?V2 ...) EFFECT_FORMULA)`

## Examples

### Standard example from the internet[1]

Domain File:

```
 
(define (domain gripper-strips)
   (:predicates (room ?r)
		(ball ?b)
		(gripper ?g)
		(at-robby ?r)
		(at ?b ?r)
		(free ?g)
		(carry ?o ?g))

   (:action move
       :parameters  (?from ?to)
       :precondition (and  (room ?from) (room ?to) (at-robby ?from))
       :effect (and  (at-robby ?to)
		     (not (at-robby ?from))))



   (:action pick
       :parameters (?obj ?room ?gripper)
       :precondition  (and  (ball ?obj) (room ?room) (gripper ?gripper)
			    (at ?obj ?room) (at-robby ?room) (free ?gripper))
       :effect (and (carry ?obj ?gripper)
		    (not (at ?obj ?room)) 
		    (not (free ?gripper))))


   (:action drop
       :parameters  (?obj  ?room ?gripper)
       :precondition  (and  (ball ?obj) (room ?room) (gripper ?gripper)
			    (carry ?obj ?gripper) (at-robby ?room))
       :effect (and (at ?obj ?room)
		    (free ?gripper)
		    (not (carry ?obj ?gripper)))))

```

Problem File:

```
define (problem strips-gripper4)
   (:domain gripper-strips)
   (:objects rooma roomb 
      ball1 ball2 ball3 ball4 
      	      left right)
   (:init (room rooma)
          (room roomb)
          (ball ball1)
          (ball ball2)
          (ball ball3)
          (ball ball4)
          (gripper left)
          (gripper right)
          (at-robby rooma)
          (free left)
          (free right)
          (at ball1 rooma)
          (at ball2 rooma)
          (at ball3 rooma)
          (at ball4 rooma))
   (:goal (and (at ball1 roomb)
               (at ball2 roomb)
               (at ball3 roomb)
   (at ball4 roomb))))

```

Gripper task with four balls:

There is a robot that can move between two rooms and pick up or drop balls with either of
his two arms. Initially, all balls and the robot are in the first room. We want the balls to be in
the second room.

Objects: The two rooms, four balls and two robot arms.

* Predicates: Is x a room? Is x a ball? Is ball x inside room y? Is robot arm x empty? [...]
* Initial state: All balls and the robot are in the first room. All robot arms are empty. [...]
* Goal specification: All balls must be in the second room.
* Actions/Operators: The robot can move between rooms, pick up a ball or drop a ball.

[1] explains all the main components of this example.

#### Perosnal Example

Domain File:

```
; A manipulation domain. 
(define (domain arch)
  (:types generalized_loc)
  (:types location object - generalized_loc)
  (:constants else ee l0 - location)
  (:types support - location)
  (:functions (at - location ?o - object)
	      (robot-at - generalized_loc))
  (:action-human move
		 :parameters (?o - object ?l - support)
		 :precondition (and (not (= (at ?o) ee))
				    (not (= ?l ee))
				    (when (not (= ?l else))
				      (forall (?other - object)
					      (not (= (at ?other) ?l)))))
		 :effect (= (at ?o) ?l))
  (:action-robot grasp
		 :parameters (?o - object ?l - location)
		 :precondition (and (forall (?other - object)
					    (not (= (at ?other) ee)))
				    (= (at ?o) ?l)
				    (= (robot-at) ?o))
		 :effect (and (= (at ?o) ee) (= (robot-at) ?l)))
  (:action-robot place
		 :parameters (?o - object ?l -location)
		 :precondition (and (forall (?other - object)
					    (not (= (at ?other) ?l)))
				    (= (at ?o) ee)
				    (= (robot-at) ?l))
		 :effect (and (= (at ?o) ?l) (= (robot-at) ?o)))
  (:action-robot transit
		 :parameters (?n - object)
		 :precondition (forall (?other - object)
					(not (= (at ?other) ee)))
		 :effect (= (robot-at) ?n))
  (:action-robot transfer
		 :parameters (?n - location)
		 :precondition (and (not (= ?n ee))
				    (not (= ?n else))
				    (exists (?other - object)
					    (= (at ?other) ee)))
		 :effect (= (robot-at) ?n))
```

Problem File:

```
; Manipulation domain
(define (problem manip_problem)
    (domain arch)
  (:objects l1 l2 l3 l4 - support)
  (:objects o0 o1 o2 - object)
  (:init (= (robot-at) else) (= (at o0) else) (= (at o1) else) (= (at o2) else))
  (:goal (and (eventually (exists (?o - object) (= (at ?o) l0)))
	      (always (imply (exists (?o - object) (= (at ?o) l0))
			       (and (exists (?o - object) (= (at ?o) l1))
				    (exists (?o - object) (= (at ?o) l2)))))))
)

```

**Explanation**:

The domain file is written in ADL format. The name of the problem domain is `arch`. Which defines the following types : 

	1. `Generalized_loc`
	2. `Location` and `object` are inherited from `generalized_loc`
	3. `Support` is inherited from `location`

`Else, ee, and l0` are constants of type `location`. We then define two function :

	1. `at`- with return type `location` and takes in argument of type `object`: a function to get the location of an object
	2. `Robot-at` - with return type `generalized_loc` and does not take any argument: a function to determine the current location of the robot 

We then define 5 different actions:

	1. Move : takes in two arguments of type object and support. 
		* The precondition is the location of the object and the ee value is not the same and the ee location and the support location should not be the same and when the condition (support location is not equal to ee location) is true then for all objects assert that location of the objects is not equal to support location. 
		* The effect : the location of the object and the support should be same
	2. Grasp : takes in two arguments of type object and location
		* The precondition is that the location of all the other objects should not be equal to the given location of the ee.
		* The Effect : the location of the object and the ee location should be equal and location of the robot should be the same as the location given.  
	3. Place : takes in two arguments of type object and location
		* The precondition is that for all the other objects the location of the objects should not be equal to the given location and the location of the object should be the same as the ee location and the robot should be at the given location. 
		* The Effect : the location of the object and the given location should be same and the robot should be at the given object location.
	4. Transit : takes in an argument of type object.
		* The precondition is that for all the other objects the location of the objects should not be equal to ee location. 
		* The Effect : location of the robot and that of the given object should be same
	5. Transfer: takes in an arguments of type location
		* The precondition is that the given location and the ee location or the else location should not be the same. And there exist other object location such that their location should be equal to the ee location. 
		* The Effect : The location of the robot should be the same as the given location.


The problem file instantiates the domain file with start and goal state. The name of the problem here is `manip_problem` defined over the domain `arch`.

Here we define 4 objects of type `support` and 3 objects of type `object`. Note: type `object` and `support` are primarily `locations`. The initial states is that the robot is at the `else` location and object o0, o1, and o2 are at location `else` too. 

The goal is that eventually there exists an object at location `l0` and only after an object is placed at location `l0` can we then place an object at location `l1` and only after that can an object exist at location `l2`. 


## References

> [1] http://www.cs.toronto.edu/~sheila/2542/w09/A1/introtopddl2.pdf


## Further Reading

Please refer to these links that I found useful to make these notes. Thanks.

> [2] http://users.cecs.anu.edu.au/~patrik/pddlman/writing.html
> [3] http://pddl4j.imag.fr/repository/wiki/BNF-PDDL-3.1.pdf