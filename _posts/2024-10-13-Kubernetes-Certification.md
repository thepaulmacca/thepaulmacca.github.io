---
title: Kubernetes Certification Resources
categories: [Kubernetes, Certifications]
tags: [kubernetes, certifications]
---

In this short post, I'm going to share some resources that you may find useful if you're thinking of pursuing any of the Kubernetes certifications soon. These are what have worked for me and many others.

Some people don't value certifications all that much - and that's fine, we all have our opinion on them. For me, they provide a structured learning path, and an opportunity to learn some new skills.

I like the Kubernetes certifications in particular as they're performance-based, you can't wing them. You have to be proficient in all areas of the curriculum.

# Curriculum

Before you get going, have a look at the [curriculum repo](https://github.com/cncf/curriculum) and grab a PDF for the exam you're looking to take.

I tend to like having a local copy to refer to as a reference, to make sure I'm focusing in on the right areas.

# Local Cluster

As mentioned earlier, these are performance-based exams - so it goes without saying that you should have a local cluster for learning, practice and experimentation. It will also help build your confidence, you can break/fix whatever you want to.

I tend to use [kind](https://kind.sigs.k8s.io/), as I find it relatively lightweight and flexible with it's configuration options.

> From the [v.0.24.0 release](https://github.com/kubernetes-sigs/kind/releases/tag/v0.24.0) it includes Network Policy support, so you no longer need to use a CNI plugin like Calico for basic scenarios.
{: .prompt-info }

# Certified Kubernetes Administrator (CKA)

I found the Udemy [Certified Kubernetes Administrator (CKA) with Practice Tests](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/) course to be the best for this exam. The fact that you also get access to practice tests with it aswell makes it worth the money in my opinion.

## CKA Killer Shell

It's also worth going through the [Killer Shell scenarios](https://killercoda.com/cka). These are free, but have a time limit.

# Certified Kubernetes Application Developer (CKAD)

I found the Udemy [Kubernetes Certified Application Developer (CKAD) with Tests](https://www.udemy.com/course/certified-kubernetes-application-developer/) course to be the best for this exam aswell. Similar to the CKA course, you also get access to practice tests.

## CKAD Killer Shell

Similar to the CKA, it's also worth going through the [Killer Shell scenarios](https://killercoda.com/killer-shell-ckad).

# Certified Kubernetes Security Specialist (CKS)

I've found this exam to be the toughest of them all (it's quite well known for that), and am still yet to pass it. I'm going to keep trying though, but have had to take a step back for now for a bit of a mental break 😅.

The lack of material for this exam is quite surprising compared to the CKA/CKAD given how tough it is, but a lot of people refer to this [YouTube CKS](https://youtu.be/d9xfB5qaOfg?si=xPbBVR9_Pts6kk-p) course by Killer Shell. It's **11 hours** long, so you'll have to break it down into small chunks as there's _a lot_ to take in.

> This course hasn't been updated for a while now, and given the [upcoming changes to the exam](https://training.linuxfoundation.org/cks-program-changes/), you'll only be able to pick out certain parts of it.
{: .prompt-warning }

## CKS Killer Shell

You should also go through the [Killer Shell scenarios](https://killercoda.com/killer-shell-cks) for this exam aswell.

## CKS Study Guide

I've also been reading this [study guide by Benjamin Muschko](https://amzn.eu/d/6KReKUL), but i'd say that you need to supplement it with the above YouTube course and plenty of labbing!

# Summary

Preparation is key when studying for these exams - but using these resources, you'll be in a good place. Best of luck on your Kubernetes certification journey!
