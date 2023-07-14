> [!info] 
> **Tensors** - Represents multidimensional arrays in tensorflow library. 
> - Tensors have their own object attributes.

Ways to create tensor ->
- `tf.constant(matrices, dtype)` - converts matrix into tensor of dtype
- `tf.eye(num_rows, num_columns, batch_shape, dtype, name)` - creates identity matrix or batch of matrices
- `tf.fill(dims, value)` - constructs matrix filled with scalar value
- `tf.ones(shape, dtype, name)` - constructs matrix filled with ones
- `tf.oneslike(input, dtype, name)` - creates tensor with same dimensions as input filled with ones.
- `tf.zeros`
- `tf.random.normal(shape, mean, stddev, dtype, seed, name)` - randomly generate tensor of values taken from a normal distribution.
- `tf.random.uniform(shape, ni\\minval, maxval, dtype, seed, name)`
- `tf.range(start, stop+1)`

Basic tensor methods ->
- `tf.cast(tensor, dtype)` - casting to another dtype
- `tf.shape(input_tensor)` - getting shape of tensor
- `tf.rank(input)` - rank of tensor (no. of indices needed to extract element) / order / degree / ndims
- `tf.size(input, out_type)` - get size / no. of elements in tensor
- `tensor[start:stop+1:step]` - can be indexed like lists
- `tensor[row,column]` - to access rows and columns

> [!caution] 
> For same random output across cells ->
> - `tf.random.set_seed(val)` - set global seed value 
> - assign a value to seed parameter of a method