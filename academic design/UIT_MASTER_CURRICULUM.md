# UIT_MASTER_CURRICULUM.md

> Official Academic Specification for the Master of Computer Science Handbook (MCSH)

---

# Metadata

| Field | Value |
|-------|-------|
| Document | UIT_MASTER_CURRICULUM.md |
| Version | 3.0.0 |
| Status | Frozen after validation |
| Language | English |
| Purpose | Academic Specification |
| Scope | Master of Computer Science |
| University | University of Information Technology (UIT) |
| Used By | CURRICULUM_MAPPING.md, MASTER_SYSTEM_PROMPT.md |

---

# 1. Purpose

This document defines the academic specification for the Master Computer Science Handbook.

It is the **Single Source of Truth (SSOT)** for all curriculum-related information.

Every handbook volume, chapter, project, exercise, and research topic must ultimately trace back to this specification.

This document is intentionally independent of any AI model.

It serves as the permanent academic backbone of the project.

---

# 2. Educational Vision

The handbook is designed for software engineers transitioning into graduate-level Computer Science.

The educational journey is:

Software Engineering

↓

Computer Science Foundation

↓

Advanced Computer Science

↓

Artificial Intelligence

↓

Scientific Research

↓

Master's Thesis

↓

Independent Research

The objective is not merely to pass university courses.

The objective is to cultivate engineers capable of understanding, applying, and creating Computer Science knowledge.

---

# 3. Curriculum Design Principles

The curriculum follows these principles.

## Foundation First

Strong mathematical and Computer Science foundations precede advanced topics.

---

## Knowledge Progression

Every concept builds upon previous knowledge.

No isolated topics.

---

## Theory Before Practice

Understand principles before implementation.

---

## Engineering Before Research

Research capability is built upon engineering competence.

---

## Concept Reuse

Knowledge should never be duplicated.

Later courses extend earlier concepts.

---

## Lifelong Learning

The curriculum prepares learners for continuous learning beyond graduation.

---

# 4. Curriculum Structure

The curriculum consists of four academic groups.

| Group | Description |
|---------|------------|
| Foundation | Mathematics and Computer Science fundamentals |
| Core | Mandatory graduate Computer Science courses |
| Elective | Specialized topics |
| Research | Research methodology and thesis |

---

# 5. Course Record Standard

Every course must follow the COURSE_SCHEMA.md specification.

Each course contains the following information.

```yaml
Metadata

Academic Information

Learning Objectives

Learning Outcomes

Prerequisites

Knowledge Units

Supporting Mathematics

Programming Requirements

Engineering Applications

Research Applications

Projects

Research Papers

Books

Assessment

Volume Mapping

Chapter Mapping

Related Courses
```

---

# 6. Official Curriculum

> This section contains the official Master of Computer Science curriculum.

## Semester 1

> Placeholder

## Semester 2

> Placeholder

## Semester 3

> Placeholder

## Thesis

> Placeholder

**Note:** Replace the placeholders with the official UIT curriculum after validation.

---

# 7. Curriculum Dependency Rules

Every course must explicitly declare:

- Required prerequisites
- Recommended background
- Subsequent courses
- Related courses

The dependency graph must remain acyclic.

Circular dependencies are prohibited.

---

# 8. Knowledge Dependency

Knowledge should progress as follows.

```text
Mathematics
        ↓
Computer Science Foundations
        ↓
Algorithms
        ↓
Computer Systems
        ↓
Artificial Intelligence
        ↓
Advanced AI
        ↓
Research Methodology
        ↓
Master's Thesis
```

---

# 9. Handbook Mapping

Every course must be mapped into the handbook.

```text
Course
        ↓
Volume
        ↓
Part
        ↓
Chapter
        ↓
Knowledge Units
```

No course should exist without a handbook mapping.

---

# 10. Course Repository

Each course is stored separately.

```text
courses/

ADVANCED_ALGORITHMS.md

MACHINE_LEARNING.md

DATA_MINING.md

DISTRIBUTED_SYSTEMS.md

COMPUTER_VISION.md

RESEARCH_METHODOLOGY.md
```

Each file follows COURSE_SCHEMA.md.

---

# 11. Graduate Competencies

After completing the handbook, learners should be able to:

- Understand graduate-level Computer Science.
- Analyze complex algorithms.
- Design scalable systems.
- Build AI applications.
- Read and evaluate research papers.
- Conduct independent research.
- Complete a master's thesis.
- Continue toward doctoral-level study.

---

# 12. Validation Checklist

Before freezing this document:

□ Official UIT curriculum imported.

□ Every course has a dedicated course file.

□ Every prerequisite verified.

□ Every course mapped to handbook volumes.

□ Every course mapped to handbook chapters.

□ No duplicate course exists.

□ No circular dependency exists.

□ Curriculum approved.

---

# 13. Related Documents

- PROJECT.md
- BOOK_STRUCTURE.md
- COURSE_SCHEMA.md
- CURRICULUM_MAPPING.md
- KNOWLEDGE_GRAPH.md
- MASTER_SYSTEM_PROMPT.md