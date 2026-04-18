%%manim -qh -r 1920,1080 --fps 30 -v WARNING ParticleInABox_Part1

from manim import *
import numpy as np

class ParticleInABox_Part1(Scene):
    def construct(self):

        # --- Create deep well geometry ---
        well_depth = 3   # controls how deep the well is
        
        left_wall = Line(LEFT*4 + DOWN*well_depth, LEFT*4 + UP*1)
        right_wall = Line(RIGHT*4 + DOWN*well_depth, RIGHT*4 + UP*1)
        bottom = Line(LEFT*4 + DOWN*well_depth, RIGHT*4 + DOWN*well_depth)
        
        box = VGroup(left_wall, right_wall, bottom)
        
        # --- Grey forbidden regions (x < 0 and x > a) ---
        well_height = well_depth + 1  # from bottom to top
        
        left_region = Rectangle(
            width=3,
            height=well_height,
            fill_color=GREY,
            fill_opacity=0.5,
            stroke_width=0
        ).next_to(left_wall, LEFT, buff=0).align_to(left_wall, DOWN)
        
        right_region = Rectangle(
            width=3,
            height=well_height,
            fill_color=GREY,
            fill_opacity=0.5,
            stroke_width=0
        ).next_to(right_wall, RIGHT, buff=0).align_to(right_wall, DOWN)
        
        # --- Labels ---
        V_inside = MathTex("V = 0").move_to(DOWN*(well_depth + 0.5))
        
        V_left = MathTex("V = \\infty").move_to(LEFT*5 + UP*2)
        V_right = MathTex("V = \\infty").move_to(RIGHT*5 + UP*2)

        # Labels
        V_inside = MathTex("V = 0").move_to(DOWN*1.5)
        V_left = MathTex("V = \\infty").next_to(left_wall, UP)
        V_right = MathTex("V = \\infty").next_to(right_wall, UP)

        self.play(FadeIn(left_region), FadeIn(right_region))
        self.play(Create(box))
        self.play(Write(V_inside), Write(V_left), Write(V_right))
        self.wait()

        # --- Classical particle ---
        dot = Dot(color=YELLOW).move_to(LEFT*2)

        self.play(FadeIn(dot))

        # Animate bouncing
        self.play(dot.animate.move_to(RIGHT*2), run_time=1)
        self.play(dot.animate.move_to(LEFT*2), run_time=1)
        self.play(dot.animate.move_to(RIGHT*2), run_time=1)

        classical_text = Text("Classical particle", font_size=28).to_edge(UP)
        self.play(Write(classical_text))
        self.wait()

        # --- Transition to quantum ---
        self.play(FadeOut(dot))

        quantum_text = Text("Quantum: described by wavefunction", font_size=28).to_edge(UP)

        self.play(Transform(classical_text, quantum_text))

        # --- Draw sine wave (n=1 state) ---
        x = np.linspace(-4, 4, 100)

        def psi(x):
            return 1.8 * np.sin(np.pi*(x+4)/8)-1

        wave = VMobject(color=BLUE)
        points = [np.array([xi, psi(xi), 0]) for xi in x]
        wave.set_points_smoothly(points)

        psi_tex = MathTex(
            r"\psi(x) = A  \cos\left(kx\right) +B \sin\left(kx\right)"
        ).scale(0.7)
        psi_tex.next_to(wave, UP, buff=0.3)
        self.play(Create(wave))
        self.wait(2.0)
        self.play(Write(psi_tex))

        self.wait(2.0)
        # Boundary condition text
        left_x = -4
        right_x = 4
        arrow = DoubleArrow(
            bottom.get_start(),
            bottom.get_end(),
            buff=0.01,
            max_tip_length_to_length_ratio=0.02
        ).shift(DOWN*0.5)
        
        arrow.set_stroke(width=3)
        arrow.set_color(YELLOW)
        
        label_a = MathTex("a").next_to(arrow, UP)
        label_a.move_to(arrow.get_center() + UP*0.3)
        label_a.set_color(YELLOW)
      
        self.play(Create(arrow), Write(label_a))
        left_point = np.array([left_x, psi(left_x), 0])
        right_point = np.array([right_x, psi(right_x), 0])
        psi_left = MathTex(r"\psi(0)=0").scale(0.7)
        psi_right = MathTex(r"\psi(a)=0").scale(0.7)
        left_dot = Dot(left_point, color=RED)
        right_dot = Dot(right_point, color=RED)
        # Position the labels near the boundary points
        psi_left.next_to(left_dot, LEFT, buff=0.2)
        psi_right.next_to(right_dot, RIGHT, buff=0.2)
        self.play(FadeIn(left_dot), FadeIn(right_dot))
        self.wait(2)
        self.play(Write(psi_left), Write(psi_right))

        self.wait(2)