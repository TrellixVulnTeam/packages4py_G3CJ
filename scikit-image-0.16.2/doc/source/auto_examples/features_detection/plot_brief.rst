.. note::
    :class: sphx-glr-download-link-note

    Click :ref:`here <sphx_glr_download_auto_examples_features_detection_plot_brief.py>` to download the full example code
.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_examples_features_detection_plot_brief.py:


=======================
BRIEF binary descriptor
=======================

This example demonstrates the BRIEF binary description algorithm. The descriptor
consists of relatively few bits and can be computed using a set of intensity
difference tests. The short binary descriptor results in low memory footprint
and very efficient matching based on the Hamming distance metric. BRIEF does not
provide rotation-invariance. Scale-invariance can be achieved by detecting and
extracting features at different scales.





.. code-block:: pytb

    Traceback (most recent call last):
      File "/home/jni/projects/scikit-image/doc/examples/features_detection/plot_brief.py", line 24, in <module>
        img2 = tf.warp(img1, tform)
      File "/home/jni/projects/scikit-image/skimage/transform/_warps.py", line 854, in warp
        warped = _warp_fast[ctype](image, matrix,
    TypeError: 'builtin_function_or_method' object is not subscriptable





.. code-block:: python

    from skimage import data
    from skimage import transform as tf
    from skimage.feature import (match_descriptors, corner_peaks, corner_harris,
                                 plot_matches, BRIEF)
    from skimage.color import rgb2gray
    import matplotlib.pyplot as plt


    img1 = rgb2gray(data.astronaut())
    tform = tf.AffineTransform(scale=(1.2, 1.2), translation=(0, -100))
    img2 = tf.warp(img1, tform)
    img3 = tf.rotate(img1, 25)

    keypoints1 = corner_peaks(corner_harris(img1), min_distance=5)
    keypoints2 = corner_peaks(corner_harris(img2), min_distance=5)
    keypoints3 = corner_peaks(corner_harris(img3), min_distance=5)

    extractor = BRIEF()

    extractor.extract(img1, keypoints1)
    keypoints1 = keypoints1[extractor.mask]
    descriptors1 = extractor.descriptors

    extractor.extract(img2, keypoints2)
    keypoints2 = keypoints2[extractor.mask]
    descriptors2 = extractor.descriptors

    extractor.extract(img3, keypoints3)
    keypoints3 = keypoints3[extractor.mask]
    descriptors3 = extractor.descriptors

    matches12 = match_descriptors(descriptors1, descriptors2, cross_check=True)
    matches13 = match_descriptors(descriptors1, descriptors3, cross_check=True)

    fig, ax = plt.subplots(nrows=2, ncols=1)

    plt.gray()

    plot_matches(ax[0], img1, img2, keypoints1, keypoints2, matches12)
    ax[0].axis('off')
    ax[0].set_title("Original Image vs. Transformed Image")

    plot_matches(ax[1], img1, img3, keypoints1, keypoints3, matches13)
    ax[1].axis('off')
    ax[1].set_title("Original Image vs. Transformed Image")


    plt.show()

**Total running time of the script:** ( 0 minutes  0.000 seconds)


.. _sphx_glr_download_auto_examples_features_detection_plot_brief.py:


.. only :: html

 .. container:: sphx-glr-footer
    :class: sphx-glr-footer-example



  .. container:: sphx-glr-download

     :download:`Download Python source code: plot_brief.py <plot_brief.py>`



  .. container:: sphx-glr-download

     :download:`Download Jupyter notebook: plot_brief.ipynb <plot_brief.ipynb>`


.. only:: html

 .. rst-class:: sphx-glr-signature

    `Gallery generated by Sphinx-Gallery <https://sphinx-gallery.readthedocs.io>`_
