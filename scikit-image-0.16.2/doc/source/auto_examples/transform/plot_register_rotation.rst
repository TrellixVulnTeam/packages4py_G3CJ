.. note::
    :class: sphx-glr-download-link-note

    Click :ref:`here <sphx_glr_download_auto_examples_transform_plot_register_rotation.py>` to download the full example code
.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_examples_transform_plot_register_rotation.py:


===================================
Polar and Log-Polar Transformations
===================================

Rotation differences between two images can be converted to translation
differences along the angular coordinate (:math:`\theta`) axis of the
polar-transformed images. Scaling differences can be converted to translation
differences along the radial coordinate (:math:`\rho`) axis if it
is first log transformed (i.e., :math:`\rho = \ln\sqrt{x^2 + y^2}`). Thus,
in this example, we use phase correlation (``feature.register_translation``)
to recover rotation and scaling differences between two images that share a
center point.


Recover rotation difference with a polar transform
==================================================



.. code-block:: python


    import numpy as np
    import matplotlib.pyplot as plt

    from skimage import data
    from skimage.feature import register_translation
    from skimage.transform import warp_polar, rotate
    from skimage.util import img_as_float

    radius = 705
    angle = 35
    image = data.retina()
    image = img_as_float(image)
    rotated = rotate(image, angle)
    image_polar = warp_polar(image, radius=radius, multichannel=True)
    rotated_polar = warp_polar(rotated, radius=radius, multichannel=True)

    fig, axes = plt.subplots(2, 2, figsize=(8, 8))
    ax = axes.ravel()
    ax[0].set_title("Original")
    ax[0].imshow(image)
    ax[1].set_title("Rotated")
    ax[1].imshow(rotated)
    ax[2].set_title("Polar-Transformed Original")
    ax[2].imshow(image_polar)
    ax[3].set_title("Polar-Transformed Rotated")
    ax[3].imshow(rotated_polar)
    plt.show()

    shifts, error, phasediff = register_translation(image_polar, rotated_polar)
    print("Expected value for counterclockwise rotation in degrees: "
          f"{angle}")
    print("Recovered value for counterclockwise rotation: "
          f"{shifts[0]}")




.. code-block:: pytb

    Traceback (most recent call last):
      File "/home/jni/projects/scikit-image/doc/examples/transform/plot_register_rotation.py", line 32, in <module>
        rotated = rotate(image, angle)
      File "/home/jni/projects/scikit-image/skimage/transform/_warps.py", line 387, in rotate
        mode=mode, cval=cval, clip=clip, preserve_range=preserve_range)
      File "/home/jni/projects/scikit-image/skimage/transform/_warps.py", line 860, in warp
        dims.append(_warp_fast[ctype](image[..., dim], matrix,
    TypeError: 'builtin_function_or_method' object is not subscriptable




Recover rotation and scaling differences with log-polar transform
=================================================================



.. code-block:: python


    from skimage.transform import rescale

    # radius must be large enough to capture useful info in larger image
    radius = 1500
    angle = 53.7
    scale = 2.2
    image = data.retina()
    image = img_as_float(image)
    rotated = rotate(image, angle)
    rescaled = rescale(rotated, scale, multichannel=True)
    image_polar = warp_polar(image, radius=radius,
                             scaling='log', multichannel=True)
    rescaled_polar = warp_polar(rescaled, radius=radius,
                                scaling='log', multichannel=True)

    fig, axes = plt.subplots(2, 2, figsize=(8, 8))
    ax = axes.ravel()
    ax[0].set_title("Original")
    ax[0].imshow(image)
    ax[1].set_title("Rotated and Rescaled")
    ax[1].imshow(rescaled)
    ax[2].set_title("Log-Polar-Transformed Original")
    ax[2].imshow(image_polar)
    ax[3].set_title("Log-Polar-Transformed Rotated and Rescaled")
    ax[3].imshow(rescaled_polar)
    plt.show()

    # setting `upsample_factor` can increase precision
    tparams = register_translation(image_polar, rescaled_polar, upsample_factor=20)
    shifts, error, phasediff = tparams
    shiftr, shiftc = shifts[:2]

    # Calculate scale factor from translation
    klog = radius / np.log(radius)
    shift_scale = 1 / (np.exp(shiftc / klog))

    print(f"Expected value for cc rotation in degrees: {angle}")
    print(f"Recovered value for cc rotation: {shiftr}")
    print()
    print(f"Expected value for scaling difference: {scale}")
    print(f"Recovered value for scaling difference: {shift_scale}")

**Total running time of the script:** ( 0 minutes  0.000 seconds)


.. _sphx_glr_download_auto_examples_transform_plot_register_rotation.py:


.. only :: html

 .. container:: sphx-glr-footer
    :class: sphx-glr-footer-example



  .. container:: sphx-glr-download

     :download:`Download Python source code: plot_register_rotation.py <plot_register_rotation.py>`



  .. container:: sphx-glr-download

     :download:`Download Jupyter notebook: plot_register_rotation.ipynb <plot_register_rotation.ipynb>`


.. only:: html

 .. rst-class:: sphx-glr-signature

    `Gallery generated by Sphinx-Gallery <https://sphinx-gallery.readthedocs.io>`_
