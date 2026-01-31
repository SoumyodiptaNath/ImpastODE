# ImpastODE 🎨

> **"I dream my painting and I paint my dream." — Vincent van Gogh**

**ImpastODE** is a generative art tool that transforms static images into dynamic, Van Gogh-style sketches. By modeling color regions as magnetic basins of attraction, it uses **Ordinary Differential Equations (ODEs)** to simulate "brushstrokes" that flow naturally along the contours of the image.

![Scene_8x](https://github.com/user-attachments/assets/cd1669d8-6733-4619-82c9-c93e76d5a992)

## 🌟 Concept

Standard edge detection gives you outlines. **ImpastODE** gives you *flow*.

It treats an image not as a grid of pixels, but as a continuous dynamical system. The algorithm:
1.  **Segments** the image into dominant color layers.
2.  **Fits** spatial probability models (GMMs) to "understand" the shape of these color blobs.
3.  **Generates** a vector field where every color blob becomes a swirling vortex.
4.  **Simulates** particles moving through this field. The path of the particle becomes the brushstroke.

The result is a "thick," turbulent texture reminiscent of the **Impasto** technique used by Impressionist painters.

![out](https://github.com/user-attachments/assets/4c2f33d8-fde9-4d49-80db-c904939be243)

---

## 🚀 How It Works (The Math)

The core logic relies on **Locally Weighted Learning** and **Dynamical Systems**.

### 1. Color Segmentation (K-Means)
We first simplify the image into $K$ dominant colors. This creates discrete "layers" for the painting.

### 2. Spatial Clustering (Gaussian Mixture Models)
For each color layer, we fit a **Gaussian Mixture Model (GMM)** to the $(x, y)$ coordinates of the pixels. This identifies the spatial "pockets" where a specific color exists.

### 3. Vector Field Construction
We define a global dynamical system $\dot{x} = f(x)$.
For every Gaussian component $k$ (defined by mean $\mu_k$ and covariance $\Sigma_k$), we construct a local linear system:

$$\dot{x}_k = A_k (x - \mu_k)$$

The matrix $A_k$ is constructed using the **Eigenvectors** of $\Sigma_k$ to align with the shape of the blob, and the **Eigenvalues** are manipulated to create a **stable spiral** (attracting particles to the center while rotating them).

### 4. Integration (The "Painting")
The global velocity at any point $x$ is a weighted sum of these local spirals, weighted by how close the point is to a Gaussian center (Mahalanobis distance):

$$\dot{x} = \frac{\sum w_k(x) A_k (x - \mu_k)}{\sum w_k(x)}$$

We solve this using `scipy.integrate.solve_ivp`. The trajectory of the solution is plotted as a curve, colored by the cluster it belongs to.

---

## 📦 Installation

1.  **Clone the repository**
    ```bash
    git clone [https://github.com/your-username/ImpastODE.git](https://github.com/your-username/ImpastODE.git)
    cd ImpastODE
    ```

2.  **Install dependencies**
    It is recommended to use a virtual environment.
    ```bash
    pip install numpy matplotlib opencv-python scipy scikit-learn
    ```

---

## 🎨 Usage

1.  Place your target image in the project folder (e.g., `Scene.jpg`).
2.  Run the script:

    ```bash
    python main.py
    ```

3.  **Interactive Mode:**
    * The script first generates a static batch of trajectories.
    * Close the first window to open the **Live Canvas**.
    * **Hover your mouse** over the blank canvas on the left. The tool will generate trajectories in real-time starting from your cursor position!

### Configuration
You can tweak the artistic style by modifying the `LocallyWeightedLinearModelColor` initialization in `main.py`:

```python
LWLM = LocallyWeightedLinearModelColor(
    num_colors=5,             # More colors = more detailed palette
    image_size=500,           # Resolution of the simulation field
    sim_t_max=50.0,           # Length of the brushstrokes
    num_gauss_per_color=4,    # Complexity of shapes per color (Higher = More blobs of same color)
    Omega_Magnitude=2.5e-2,   # Tightness of the spirals (Higher = more "Starry Night")
)
```

---

## 🤝 Contributing

Pull requests are welcome! Ideas for future features:

* **Directional alignment:** Align spirals based on image gradients (Sobel) rather than just covariance.
* **Stroke width variation:** Change line thickness based on velocity.
* **Video export:** Animate the drawing process.

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.


---

