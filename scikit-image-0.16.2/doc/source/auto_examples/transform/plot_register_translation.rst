.. note::
    :class: sphx-glr-download-link-note

    Click :ref:`here <sphx_glr_download_auto_examples_transform_plot_register_translation.py>` to download the full example code
.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_examples_transform_plot_register_translation.py:


=====================================
Cross-Correlation (Phase Correlation)
=====================================

In this example, we use phase correlation to identify the relative shift
between two similar-sized images.

The ``register_translation`` function uses cross-correlation in Fourier space,
optionally employing an upsampled matrix-multiplication DFT to achieve
arbitrary subpixel precision [1]_.

.. [1] Manuel Guizar-Sicairos, Samuel T. Thurman, and James R. Fienup,
       "Efficient subpixel image registration algorithms," Optics Letters 33,
       156-158 (2008). :DOI:`10.1364/OL.33.000156`





.. rst-class:: sphx-glr-horizontal


    *

      .. image:: /auto_examples/transform/images/sphx_glr_plot_register_translation_001.png
            :class: sphx-glr-multi-img

    *

      .. image:: /auto_examples/transform/images/sphx_glr_plot_register_translation_002.png
            :class: sphx-glr-multi-img


.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

    Known offset (y, x): (-22.4, 13.32)
    Detected pixel offset (y, x): [ 22. -13.]
    Detected subpixel offset (y, x): [ 22.4  -13.32]




|


.. code-block:: python

    import numpy as np
    import matplotlib.pyplot as plt

    from skimage import data
    from skimage.feature import register_translation
    from skimage.feature.register_translation import _upsampled_dft
    from scipy.ndimage import fourier_shift

    image = data.camera()
    shift = (-22.4, 13.32)
    # The shift corresponds to the pixel offset relative to the reference image
    offset_image = fourier_shift(np.fft.fftn(image), shift)
    offset_image = np.fft.ifftn(offset_image)
    print(f"Known offset (y, x): {shift}")

    # pixel precision first
    shift, error, diffphase = register_translation(image, offset_image)

    fig = plt.figure(figsize=(8, 3))
    ax1 = plt.subplot(1, 3, 1)
    ax2 = plt.subplot(1, 3, 2, sharex=ax1, sharey=ax1)
    ax3 = plt.subplot(1, 3, 3)

    ax1.imshow(image, cmap='gray')
    ax1.set_axis_off()
    ax1.set_title('Reference image')

    ax2.imshow(offset_image.real, cmap='gray')
    ax2.set_axis_off()
    ax2.set_title('Offset image')

    # Show the output of a cross-correlation to show what the algorithm is
    # doing behind the scenes
    image_product = np.fft.fft2(image) * np.fft.fft2(offset_image).conj()
    cc_image = np.fft.fftshift(np.fft.ifft2(image_product))
    ax3.imshow(cc_image.real)
    ax3.set_axis_off()
    ax3.set_title("Cross-correlation")

    plt.show()

    print(f"Detected pixel offset (y, x): {shift}")

    # subpixel precision
    shift, error, diffphase = register_translation(image, offset_image, 100)

    fig = plt.figure(figsize=(8, 3))
    ax1 = plt.subplot(1, 3, 1)
    ax2 = plt.subplot(1, 3, 2, sharex=ax1, sharey=ax1)
    ax3 = plt.subplot(1, 3, 3)

    ax1.imshow(image, cmap='gray')
    ax1.set_axis_off()
    ax1.set_title('Reference image')

    ax2.imshow(offset_image.real, cmap='gray')
    ax2.set_axis_off()
    ax2.set_title('Offset image')

    # Calculate the upsampled DFT, again to show what the algorithm is doing
    # behind the scenes.  Constants correspond to calculated values in routine.
    # See source code for details.
    cc_image = _upsampled_dft(image_product, 150, 100, (shift*100)+75).conj()
    ax3.imshow(cc_image.real)
    ax3.set_axis_off()
    ax3.set_title("Supersampled XC sub-area")


    plt.show()

    print(f"Detected subpixel offset (y, x): {shift}")

**Total running time of the script:** ( 0 minutes  0.248 seconds)


.. _sphx_glr_download_auto_examples_transform_plot_register_translation.py:


.. only :: html

 .. container:: sphx-glr-footer
    :class: sphx-glr-footer-example



  .. container:: sphx-glr-download

     :download:`Download Python source code: plot_register_translation.py <plot_register_translation.py>`



  .. container:: sphx-glr-download

     :download:`Download Jupyter notebook: plot_register_translation.ipynb <plot_register_translation.ipynb>`


.. only:: html

 .. rst-class:: sphx-glr-signature

    `Gallery generated by Sphinx-Gallery <https://sphinx-gallery.readthedocs.io>`_
