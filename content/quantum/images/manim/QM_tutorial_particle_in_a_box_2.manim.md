%%manim -qh -r 1920,1080 --fps 60 -v WARNING ParticleInABox_Part1


from manim import *
import numpy as np

class ParticleInABox_Part1(Scene):
    def construct(self):

        # --- WELL SETUP ---
        well_depth = 3

        left_wall = Line(LEFT*4 + DOWN*well_depth, LEFT*4 + UP*1)
        right_wall = Line(RIGHT*4 + DOWN*well_depth, RIGHT*4 + UP*1)
        bottom = Line(LEFT*4 + DOWN*well_depth, RIGHT*4 + DOWN*well_depth)

        box = VGroup(left_wall, right_wall, bottom)

        # Forbidden regions
        well_height = well_depth + 1

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

        # Labels
        V_inside = MathTex("V = 0").move_to(DOWN*1.5)
        V_left = MathTex("V = \\infty").next_to(left_wall, UP)
        V_right = MathTex("V = \\infty").next_to(right_wall, UP)
        # --- WIDTH a ---
        arrow = DoubleArrow(
            bottom.get_start(),
            bottom.get_end(),
            buff=0.01,
            max_tip_length_to_length_ratio=0.02
        ).shift(DOWN*0.5)

        arrow.set_stroke(width=3)
        arrow.set_color(YELLOW)

        label_a = MathTex("a").set_color(YELLOW)
        label_a.move_to(arrow.get_center() + UP*0.3)

        self.add(left_region, right_region)
        self.add(box)
        self.add(V_inside, V_left, V_right)
        self.add(arrow, label_a)
        # self.wait(1)

        title_text = Text("Stationary States", font_size=32)
        
        title_eq = MathTex(
            r"\psi_n(x) = \sqrt{\frac{2}{a}}\sin\left(\frac{n\pi x}{a}\right), \quad E_n \propto n^2"
        ).scale(0.7)
        
        # stack them vertically
        title_group = VGroup(title_text, title_eq).arrange(DOWN, buff=0.2)
        
        title_group.to_edge(UP)
        
        self.play(Write(title_group))


        # self.wait(1)

        # --- EIGENSTATES ---
        levels_y = [-3.0, -1.2, 0.8]
        n_values = [1, 2, 3]

        all_states = VGroup()

        for i, n in enumerate(n_values):

            y_shift = levels_y[i]

            # baseline
            base_line = Line(
                LEFT*4 + UP*y_shift,
                RIGHT*4 + UP*y_shift,
                stroke_width=2.5
            )

            # wavefunction
            def psi_n(x, n=n):
                return 0.6 * np.sin(n * np.pi * (x+4)/8)

            x_vals = np.linspace(-4, 4, 200)

            wave_n = VMobject(color=BLUE)
            points = [
                np.array([xi, psi_n(xi) + y_shift, 0])
                for xi in x_vals
            ]
            wave_n.set_points_smoothly(points)
            wave_n.set_stroke(width=2)

            # labels (left side)
            n_label = MathTex(f"n={n}").scale(0.6)
            n_label.next_to(base_line, LEFT, buff=0.3)
            
            # full wavefunction expression
            psi_expr = MathTex(
                rf"\psi_{n}(x)=\sqrt{{\frac{{2}}{{a}}}}\sin\left(\frac{{{n}\pi x}}{{a}}\right)"
            ).scale(0.5)
            
            psi_expr.next_to(base_line, RIGHT, buff=0.3)
            
            # energy label
            E_label = MathTex(rf"E_{n}").scale(0.6)
            E_label.next_to(psi_expr, RIGHT, buff=0.3)
            
            group = VGroup(base_line, wave_n, n_label, psi_expr, E_label)
            all_states.add(group)

        # animate nicely
        for g in all_states:
            self.play(Create(g))
            self.wait(2)

        self.wait(1)
        prob_states = VGroup()

        for i, n in enumerate(n_values):
        
            y_shift = levels_y[i]
        
            # probability density
            def prob_n(x, n=n):
                return 0.6 * (np.sin(n * np.pi * (x+4)/8))**2
        
            x_vals = np.linspace(-4, 4, 200)
        
            prob_wave = VMobject(color=GREEN)
            points = [
                np.array([xi, prob_n(xi) + y_shift, 0])
                for xi in x_vals
            ]
            prob_wave.set_points_smoothly(points)
            prob_wave.set_stroke(width=2)
        
            prob_states.add(prob_wave)
          
        prob_exprs = []
        for n in n_values:
        
            prob_expr = MathTex(
                rf"|\psi_{n}(x)|^2=\frac{{2}}{{a}}\sin^2\left(\frac{{{n}\pi x}}{{a}}\right)"
            ).scale(0.5)
        
            prob_exprs.append(prob_expr)
        for i, g in enumerate(all_states):
            prob_exprs[i].move_to(g[3].get_center())
        original_exprs = [g[3] for g in all_states]

      
        new_title_eq = MathTex(
            r"|\psi_n(x)|^2"
        ).scale(0.7)
        
        new_title_group = VGroup(title_text, new_title_eq).arrange(DOWN, buff=0.2).to_edge(UP)
        original_waves = [g[1] for g in all_states]
        self.play(
              Transform(title_group, new_title_group),   # ✅ add this
              *[
                  Transform(original_waves[i], prob_states[i])
                  for i in range(len(original_waves))
              ],
              *[
                  Transform(original_exprs[i], prob_exprs[i])
                  for i in range(len(original_exprs))
              ],
              run_time=3
         )
          
        self.wait(2)