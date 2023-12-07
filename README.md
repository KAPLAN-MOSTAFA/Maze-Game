// This is the main form class for the maze game application.
// It handles the movement of the player within the maze, tracks time, and detects collisions with walls and the goal.

using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using static System.Windows.Forms.VisualStyles.VisualStyleElement.ToolTip;

namespace Labirent_Oyunu
{
    public partial class Form1 : Form
    {
        // Variables to keep track of player's position, step size, rotation, and movement directions.
        int step = 28;
        int x = 38, y = 12;
        int rotate = 1;
        string[] Dir = { "Up", "Left", "Down", "Right", "Up" };
        int i = 2;
        int minute = 0;
        int second = 0;

        // Constructor for the form.
        public Form1()
        {
            InitializeComponent();
        }

        // Checks if the player collides with a wall.
        public Boolean test()
        {
            foreach (Control c in this.Controls)
            {
                if (c is Label && (string)c.Tag == "wall")
                {
                    if (pictureBox2.Bounds.IntersectsWith(c.Bounds))
                    {
                        return true;
                    }
                }
            }
            return false;
        }

        // Handles player movement based on the provided direction.

        public Boolean Moving(string Dir, PictureBox pictureBox2)
        {
            // Variable to track the validity of the movement.
            bool IsValied = true;

            // Switch statement to handle movement based on the provided direction.
            switch (Dir)
            {
                case "Down":
                    // Move the player down and check for collisions with walls.
                    pictureBox2.Location = new Point(x, y += step);
                    if (test())
                    {
                        pictureBox2.Location = new Point(x, y -= step);
                        IsValied = false;
                    }

                    // Rotate the player's image if necessary.
                    if (rotate != 1 && !test())
                    {
                        Image img = pictureBox2.Image;
                        img.RotateFlip(RotateFlipType.Rotate90FlipNone);
                        if (rotate == 2) img.RotateFlip(RotateFlipType.Rotate90FlipNone);
                        else if (rotate == 3) img.RotateFlip(RotateFlipType.Rotate180FlipNone);
                        pictureBox2.Image = img;
                        rotate = 1;
                    }
                    return IsValied;

                case "Up":
                    // Move the player up and check for collisions with walls.
                    pictureBox2.Location = new Point(x, y -= step);
                    if (test())
                    {
                        pictureBox2.Location = new Point(x, y += step);
                        IsValied = false;
                    }

                    // Rotate the player's image if necessary.
                    if (rotate != 2 && !test())
                    {
                        Image img = pictureBox2.Image;
                        img.RotateFlip(RotateFlipType.Rotate270FlipNone);
                        if (rotate == 1) img.RotateFlip(RotateFlipType.Rotate270FlipNone);
                        else if (rotate == 3) img.RotateFlip(RotateFlipType.Rotate180FlipNone);
                        pictureBox2.Image = img;
                        rotate = 2;
                    }
                    return IsValied;

                case "Left":
                    // Move the player left and check for collisions with walls.
                    pictureBox2.Location = new Point(x -= step, y);
                    if (test())
                    {
                        pictureBox2.Location = new Point(x += step, y);
                        IsValied = false;
                    }

                    // Rotate the player's image if necessary.
                    if (rotate != 3 && !test())
                    {
                        Image img = pictureBox2.Image;
                        img.RotateFlip(RotateFlipType.Rotate90FlipNone);
                        if (rotate == 4) img.RotateFlip(RotateFlipType.Rotate90FlipNone);
                        else if (rotate == 2) img.RotateFlip(RotateFlipType.Rotate180FlipNone);
                        pictureBox2.Image = img;
                        rotate = 3;
                    }
                    return IsValied;

                case "Right":
                    // Move the player right and check for collisions with walls.
                    pictureBox2.Location = new Point(x += step, y);
                    if (test())
                    {
                        pictureBox2.Location = new Point(x -= step, y);
                        IsValied = false;
                    }

                    // Rotate the player's image if necessary.
                    if (rotate != 4 && !test())
                    {
                        Image img = pictureBox2.Image;
                        img.RotateFlip(RotateFlipType.Rotate270FlipNone);
                        if (rotate == 3) img.RotateFlip(RotateFlipType.Rotate270FlipNone);
                        else if (rotate == 2) img.RotateFlip(RotateFlipType.Rotate180FlipNone);
                        pictureBox2.Image = img;
                        rotate = 4;
                    }
                    return IsValied;
            }

            // Default case, should not be reached. Returns the validity of the movement.
            return IsValied;
        }


        // Event handler for the start button click.
        private void label33_Click(object sender, EventArgs e)
        {
            label33.Visible = false;
            TimerRand.Enabled = true;
            label35.Visible = true;
            second = 0;
            minute = 0;
            label34.Text = minute.ToString() + ":" + second.ToString();
        }

        // Event handler for the timer tick event.
        private void TimerRand_Tick(object sender, EventArgs e)
        {
            second++;
            if (second == 60)
            {
                second = 0;
                minute++;
            }
            label34.Text = minute.ToString() + ":" + second.ToString();
            if (Moving(Dir[i - 1], pictureBox2))
            {
                if (i - 1 == 2) { i--; }
                else if (i - 1 == 1) { i--; }
                else if (i - 1 == 0) { i = 4; }
                else if (i - 1 == 3) { i--; }
            }
            else if (!Moving(Dir[i], pictureBox2))
            {
                i++;
                if (i == 5) i = 2;
            }
            if (pictureBox2.Bounds.IntersectsWith(pictureBox1.Bounds))
            {
                TimerRand.Enabled = false;
                MessageBox.Show("Congratulations, you won!");
                x = 38;
                y = 12;
                rotate = 1;
                pictureBox2.Location = new Point(x, y);
                label33.Visible = true;
                second = 0;
                minute = 0;
                label34.Text = minute.ToString() + ":" + second.ToString();
            }
        }

        // Event handler for the stop button click.
        private void label35_Click(object sender, EventArgs e)
        {
            label33.Visible = true;
            TimerRand.Enabled = false;
            label35.Visible = false;
        }

        // Event handler for keyboard input.
        private void Form1_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.KeyData == Keys.Down)
            {
                Moving("Down", pictureBox2);
            }
            else if (e.KeyData == Keys.Up)
            {
                Moving("Up", pictureBox2);
            }
            else if (e.KeyData == Keys.Left)
            {
                Moving("Left", pictureBox2);
            }
            else if (e.KeyData == Keys.Right)
            {
                Moving("Right", pictureBox2);
            }
            if (pictureBox2.Bounds.IntersectsWith(pictureBox1.Bounds))
            {
                MessageBox.Show("Congratulations, you won!");
                x = 38;
                y = 12;
                rotate = 1;
                pictureBox2.Location = new Point(x, y);
                TimerRand.Enabled = false;
                label33.Visible = true;
            }
        }
    }
}



