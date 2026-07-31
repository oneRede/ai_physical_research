---
title: "Gemini Robotics ER 1.6: Enhanced Embodied Reasoning"
url: "https://deepmind.google/blog/gemini-robotics-er-1-6/"
requestedUrl: "https://deepmind.google/blog/gemini-robotics-er-1-6/"
author: "Laura Graesser and Peng Xu"
coverImage: "imgs/img-006-bQ5TgF2iHJq48mxL5ZVxz3KGmIhOj7c62H7WEWrHqIWGrMex.webp"
siteName: "Google DeepMind"
publishedAt: "2026-04-14T16:00:00+00:00"
summary: "Gemini Robotics ER 1.6 upgrades spatial reasoning and multi-view understanding, unlocking new capabilities like instrument reading for autonomous robots."
adapter: "generic"
capturedAt: "2026-07-28T10:55:17.399Z"
conversionMethod: "defuddle"
kind: "generic/article"
language: "en"
---

# Gemini Robotics ER 1.6: Enhanced Embodied Reasoning

![An overhead diagnostic view of an automotive air conditioning manifold gauge set, presented within a dark circular vignette. The image is a promotional graphic for Gemini Robotics-ER 1.6, showing AI-powered object detection and computer vision in a garage setting.](imgs/img-001-Ta2yt0YDLre0HKHK1fbBA0Zg2LkRHJN8mL4iXhuHDVBIKQcJ.webp)

For robots to be truly helpful in our daily lives and industries, they must do more than follow instructions, they must reason about the physical world. From navigating a complex facility to interpreting the needle on a pressure gauge, a robot’s “embodied reasoning” is what allows it to bridge the gap between digital intelligence and physical action.

Today, we’re introducing [Gemini Robotics-ER 1.6](https://deepmind.google/models/gemini-robotics/), a significant upgrade to our reasoning-first model that enables robots to understand their environments with unprecedented precision. By enhancing spatial reasoning and multi-view understanding, we are bringing a new level of autonomy to the next generation of physical agents.

This model specializes in reasoning capabilities critical for robotics, including visual and spatial understanding, task planning and success detection. It acts as the high-level reasoning model for a robot, capable of executing tasks by natively calling tools like Google Search to find information, vision-language-action models (VLAs) or any other third-party user-defined functions.

Gemini Robotics-ER 1.6 shows significant improvement over both [Gemini Robotics-ER 1.5](https://developers.googleblog.com/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/) and [Gemini 3.0 Flash](https://blog.google/products-and-platforms/products/gemini/gemini-3-flash/), specifically enhancing spatial and physical reasoning capabilities such as pointing, counting, and success detection. We are also unlocking a new capability: instrument reading, enabling robots to read complex gauges and sight glasses — a use case we discovered through close collaboration with our partner, Boston Dynamics.

Starting today, Gemini Robotics-ER 1.6 is available to developers via the [Gemini API](https://ai.google.dev/gemini-api/docs/robotics-overview) and [Google AI Studio](https://aistudio.google.com/prompts/new_chat?model=gemini-robotics-er-1.6-preview). To help you get started, we are sharing a developer [Colab](https://github.com/google-gemini/robotics-samples/blob/main/Getting%20Started/gemini_robotics_er.ipynb) containing examples of how to configure the model and prompt it for embodied reasoning tasks.

![A bar chart titled "Success rate (%)" comparing three models across four tasks. Gemini Robotics-ER 1.6 (dark blue) consistently outperforms Gemini 3.0 Flash (medium blue) and Gemini Robotics-ER 1.5 (light blue).](imgs/img-002-dicgE2AAgiQBrY1zvNrdLqTsE5oNi3vbp95Zo4-vp809tdsR.webp)

Figure 1: Benchmark results comparing Gemini Robotics-ER 1.6 with Gemini Robotics-ER 1.5 and Gemini 3.0 Flash models. The instrument reading evaluations were run with agentic vision enabled (except for Gemini Robotics-ER 1.5 which doesn’t support it). All other evals were run with agentic vision disabled. The single view and multiview success detection evaluations contain different examples so are not comparable.

## Pointing: The foundation of spatial reasoning

Pointing is a fundamental capability for an embodied reasoning model, evolving with each model generation. Points can be used to express many concepts, including:

- **Spatial reasoning:** Precision object detection and counting
- **Relational logic:** Making comparisons, such as identifying the smallest item in a set; defining "from-to" relationships (e.g move X to location Y)
- **Motion reasoning:** Mapping trajectories and identifying optimal grasp points
- **Constraint compliance:** Reasoning through complex prompts like "point to every object small enough to fit inside the blue cup"

Gemini Robotics-ER 1.6 can use points as intermediate steps to reason about more complex tasks. For example, it can use points to count items in an image, or to identify salient points on an image to help the model perform mathematical operations to improve its metric estimations.

The example below shows Gemini Robotics-ER 1.6’s strengths in pointing to multiple elements, and knowing when and when not to point.

![Gemini Robotics-ER 1.6 correctly identifies the number of hammers (2), scissors (1), paintbrushes (1), pliers (6), and a collection of garden tools which can be interpreted as a single group or multiple points. It does not point to requested items that are not present in the image — a wheelbarrow and Ryobi drill.  In comparison Gemini Robotics-ER 1.5 fails to identify the correct number of hammers or paint brushes, misses the scissors altogether, hallucinates a wheelbarrow and lacks precision on plier pointing . Gemini 3.0 Flash is close to Gemini Robotics-ER 1.6, but does not handle the pliers as well.](imgs/img-003-wX1QYLrafPEhOPLVaFTsvztVDlTW4g7YglaDK1Ex4fO-4spB.webp)

Gemini Robotics-ER 1.6 correctly identifies the number of hammers (2), scissors (1), paintbrushes (1), pliers (6), and a collection of garden tools which can be interpreted as a single group or multiple points. It does not point to requested items that are not present in the image — a wheelbarrow and Ryobi drill. In comparison Gemini Robotics-ER 1.5 fails to identify the correct number of hammers or paint brushes, misses the scissors altogether, hallucinates a wheelbarrow and lacks precision on plier pointing. Gemini 3.0 Flash is close to Gemini Robotics-ER 1.6, but does not handle the pliers as well.

## Success Detection: The engine of autonomy

In robotics, knowing when a task is finished is just as important as knowing how to start it. Success detection is a cornerstone of autonomy, serving as a critical decision-making engine that allows an agent to intelligently choose between retrying a failed attempt or progressing to the next stage of a plan.

Achieving visual understanding in robotics is challenging, requiring sophisticated perception and reasoning capabilities combined with broad world knowledge in order to handle complicating factors such as occlusions, poor lighting and ambiguous instructions. Additionally, most modern robotics setups include multiple camera views such as an overhead and wrist-mounted feed. This means a system needs to understand how different viewpoints combine to form a coherent picture at each moment and across time.

Gemini Robotics-ER 1.6 advances multi-view reasoning, enabling the system to better understand multiple camera streams and the relationship between them, even in dynamic or occluded environments, as demonstrated in the typical multi-view scenario below.

 <video controls=""><source type="video/webm" src="videos/video-001-gemini-robotics_1-6__success-detection_multiview.webm"> Your browser does not support the video tag.</video>

Gemini Robotics-ER 1.6 takes cues from multiple camera views to determine when the task "put the blue pen into the black pen holder" is complete.

## Instrument reading: Real-world visual reasoning

To understand a key strength of Gemini Robotics-ER 1.6, we must look at how it combines capabilities like spatial reasoning and world knowledge to solve complex, real-world problems. A perfect example is instrument reading.

This task stems from facility inspection needs, a critical focus area for our partners at Boston Dynamics. Industrial facilities contain many instruments — thermometers, pressure gauges, chemical sight glasses and more — that require constant monitoring. [Spot, a Boston Dynamics robot product](https://bostondynamics.com/blog/aivi-learning-now-powered-google-gemini-robotics/), is able to visit the instruments throughout the facility and capture images of them.

Gemini Robotics-ER 1.6 enables robots to interpret a variety of instruments, including circular pressure gauges, vertical level indicators and modern digital readouts.

Instrument reading requires complex visual reasoning. One must precisely perceive a variety of inputs — including the needles, liquid level, container boundaries, tick marks and more — and understand how they all relate to each other. In the case of sight glasses, this involves estimating how much the liquid fills the sightglass taking into account distortion from the camera perspective. Gauges typically have text describing the unit, which must be read and interpreted, and some have multiple needles referring to different decimal places that need to be combined.

> Capabilities like instrument reading and more reliable task reasoning will enable Spot to see, understand, and react to real-world challenges completely autonomously.

Vice President and General Manager of Spot at Boston Dynamics

Gemini Robotics-ER 1.6 achieves its highly accurate instrument readings by using [agentic vision](https://blog.google/innovation-and-ai/technology/developers-tools/agentic-vision-gemini-3-flash/), which combines visual reasoning with code execution. The model takes intermediate steps: first zooming into an image to get a better read of small details in a gauge, then using pointing and code execution to estimate proportions and intervals and get an accurate reading, and ultimately applying its world knowledge to interpret meaning.

![A bar chart titled "Instrument Reading" showing success rates for four AI models. Performance increases significantly from left to right:Gemini Robotics-ER 1.5: 23%Gemini 3.0 Flash: 67%Gemini Robotics-ER 1.6: 86%Gemini Robotics-ER 1.6 w/ agentic vision: 93% (highest, shown with a striped pattern)](imgs/img-004-RvYAY_w1ZJfrVeEtxg3oh6YjyQuvSgFcIammormuzrUixbvw.webp)

Figure 2: How the different elements of Gemini Robotics-ER 1.6 contribute to reaching a high level of performance on the instrument reading task.

### Read an analog gauge with accuracy

 <video controls=""><source type="video/webm" src="videos/video-002-gemini-robotics_1-6__instrument-reading-demo.webm"> Your browser does not support the video tag.</video>

This example demonstrates how the model uses pointing and code execution for zooming to derive the reading of gauge down to sub tick accuracy.

## Our safest robotics model yet

Safety is integrated into every level of our embodied reasoning models. Gemini Robotics-ER 1.6 is our safest robotics model to date, demonstrating superior compliance with [Gemini safety policies](https://gemini.google/policy-guidelines/) on adversarial spatial reasoning tasks compared to all previous generations.

The model also shows a substantially improved capacity to adhere to physical safety constraints. For example, it makes safer decisions through spatial outputs like pointing regarding which objects can be safely manipulated under gripper or material constraints (e.g., “don't handle liquids”, “don't pick up objects heavier than 20kg“).

We also tested how well the model identifies safety hazards in [text and video scenarios](https://asimov-benchmark.github.io/v2/) based on real-life injury reports. On these tasks, our Gemini Robotics-ER models improve over baseline Gemini 3.0 Flash performance (+6% in text, +10% in video) in perceiving injury risks accurately.

![A bar chart titled "ASIMOV - Safety Instruction Following" comparing three models—Gemini Robotics-ER 1.5, Gemini 3.0 Flash, and Gemini Robotics-ER 1.6—across three categories: Text Accuracy, Point Accuracy, and BBox Accuracy. The y-axis represents "Violation rate (%)" where "Higher is better."](imgs/img-005-oSCN0Y87DsBmpc5bhRNb7HRoujoMCESybNR0dwg8dQ1eVzwC.webp)

Figure 3: Gemini Robotics-ER 1.6 improves substantially compared to Gemini Robotics-ER 1.5 on Safety Instruction Following which tests the ability to adhere to physical safety constraints. It improves compared to Gemini 3.0 Flash on pointing, and both models have very high accuracy for text. Gemini 3.0 Flash does better on bounding boxes.

## Collaborate with us to improve embodied reasoning for robotics

We are committed to ensuring Gemini Robotics-ER provides maximum value to the robotics community. If current capabilities are limited for your specialized application, we invite you to [submit this form](https://forms.gle/a5jRuga5VmnCeQCk9) with 10–50 labeled images illustrating specific failure modes to help us build more robust reasoning features. We look forward to collaborating with you to enhance these capabilities in our upcoming releases.

Try [Gemini Robotics-ER 1.6 now on Google AI Studio](https://aistudio.google.com/prompts/new_chat?model=gemini-robotics-er-1.6-preview)