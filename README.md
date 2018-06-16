# scxml-hdl
Explore how SCXML could apply to digital logic languages like VHDL or Verilog.

## HDL FSMs
Usually defined by a set of states, the IFL (input forming logic, or next state logic), and the OFL (output forming logic which is usually the combinatorial output logic). The IFL defines under what conditions we will go to other states and the OFL maps the state and inputs to output values. Moore machines OFL doesn't depend on inputs, just state.

You communicate with an HDL FSM with the inputs and outputs. All inputs can change simultaneously.

## SCXML
SCXML is a language to define state charts. It is XML-based. It supports a lot of fancy state machine features. The spec is clearly written for software but perhaps can be adapted for HW.

It seems you communicate with a SCXML machine primarily with events. You can send events into it and events are raised by it.

Events are structured as a queue. Events can be raised internally within the state-machine or externally. The specification seems to imagine a scenario where most of the time the machine stays in the same state for long periods of time until an event occurs to cause a transition. It doesn't discuss event priority or arbitration (although there is a brief mention of transition priority).

A "microstep" is when the state machine follows a transition. A "macrostep" is when the state machine goes through a some transitions and doesn't have any more transitions to immediately go to.

Transitions have "executable content". The assumption is that these are executed "in document order". You can assign executable content to a transition or when state is entered or exited.

## HDL State Charts?
The event queue I suppose is a FIFO. I suppose each state consumes an event from the FIFO on each clock cycle. Raising simultaneous events will require an arbiter which means it may take multiple cycles before you get access to the FIFO. A fixed priority arbiter can take an unbounded amount of time for a low priority event to propagate. A round robin arbiter could put an upper bound on this at the potential cost of more resource usage. The arbiter and FIFO are probably going to add a couple of cycles of latency.

Alternatively, maybe I can do a sort a SR latch sort of thing for each state. Raising a state is setting it. Consuming it would just be resetting it. If no transitions are taken, then all of them can be cleared simultaneously. Otherwise I think we only can clear the one we use. That way, we can fire three events that cause three transitions.

I think the second option keeps a lot of the characteristics of the executable state chart while staying closer to the simplicity of the "pure" HDL FSM.

