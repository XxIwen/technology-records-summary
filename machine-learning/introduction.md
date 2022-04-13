## Introduction to Machine Learning
> This section provides a high-level overview on the processes used in machine learning.

### Prediction
Mathematically, prediction is a function f that takes in some input x and returns some output y. Examples:
- Given an image of a person as input, we want to predict the gender of the person. The output of the prediction is either “male” or “female”.
- Given an image of a face as input, we want to predict the age of the person. The output pf the prediction is either the numeric age of the face or the age band of the face.
- Given an arbitrary image as input, we want to predict the locations of the cars in the image, if any. The output of the prediction is a list of bounding boxes of the cars.

The aim is to find a good prediction function f that accurately predicts its output. Typically, the prediction function is modeled as a member from a family of functions f(x | θ), where θ is the parameters of the function. An example where inputs and parameters are in R4 is the following[ This form is known as linear regression.]:

`f(x | θ) = θ1x1 + θ2x2 + θ3x3 + θ4x4`

One may select a function from this family of functions by assigning the parameters θ to the values (0.1, 0.2, 0.3, 0.4), thereby resulting in the function

`f(x) = 0.1 x1 + 0.2 x2 + 0.3 x3 + 0.4 x4`

Given a family of functions f(x | θ), the assignment of the parameters θ to some fixed values is known as a model.

### Machine Learning
In practice, it is too difficult to manually find a good prediction function for the vast majority of cases, even if we restrict ourselves to a particular family of functions. So we turn to machine learning, with the aim of automatically finding a good model by feeding the machine learning algorithm a large number of labeled inputs. Each labeled input is one input that is annotated or labeled with the correct output, e.g., an input image with a face together with the label “male”. There are numerous machine learning algorithms available, and the descriptions of these algorithms are outside the scope of this document.

In this document, we use the term dataset to mean a collection of inputs. Through the annotation step, a dataset becomes a labeled dataset. In this document, all datasets are labeled unless otherwise specified. The process of applying a machine learning model on a dataset to obtain a good model is known as training or learning.

How do we know that a trained model is good? We take another dataset, run it through the prediction step, and compare the prediction output with the correct labels. The comparison is known as evaluation, whose output are evaluation results containing one or more numbers indicating how accurate[ There are many ways to determine how accurate a model is. Accuracy is one of them, but there are others like precision, recall, F-measure, and so on.] this model is on this dataset. To differentiate between the two datasets, we use the terms training dataset (for training a model) and testing dataset (for evaluating a model).

For some machine learning algorithms, particularly deep learning algorithms, it is possible to use an existing model in conjunction with a training dataset to train an even better model. This step is known transfer learning if the model was trained using a dataset for another problem, or fine-tuning if the model was trained using a dataset for the same problem.