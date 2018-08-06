### 2018.7.27
x code structure: code/main.py
  x parameters
  ? train
    ? model_generator2_2new6.placeholder_inputs
      x point_clouds_pl: [4,1024,6], coordinate + normal
      ? pointclouds_radius
    x model_generator2_2new6.get_gen_model
      x utils.pointnet_util.pointnet_sa_module: pointnet++ set abstraction module
      x utils.pointnet_util.pointnet_fp_module: feature propagation/interpolation layer        
      x size of features:
        all *_xyz [4, x, 3] as coordinates
        l0_points [4, 1024, 3 or 0]
        l1_points [4, 1024, 64] 
        l2_points [4, 512, 128] -> up_l2_points [4, 1024, 64]
        l3_points [4, 256, 256] -> up_l3_points [4, 1024, 64]
        l4_points [4, 128, 512] -> up_l4_points [4, 1024, 64]
      x tf_util.conv2d
    x model_utils.get_emd_loss
    x model_utils.get_repulsion_loss4
    x tf.losses.get_regularization_loss
    x model_utils.pre_load_checkpoint: if checkpoint exist, load from previous training
    x train_one_epoch

### 2018.8.5
? evaluation_code
  x compile, conda install -c conda-forge cgal
  x input
    x nicolo.off: origin mesh files, vertex coordinates + mesh vertex index
    x nicolo.xyz: upsampled points coordinates 
  x nicolo_point2mesh_distance.xyz
    x for each points in .xyz, calculate nearest distance for vertex in mesh
    x column: #points in .xyz
    x row: (x,y,z,nearest distance)
  x nicolo_sampling_seed
    x sample location for point density calculation
    x column: #sample center, depens on #threads x10
    x row: #triangle, linear combination(a,b,c) from vertex
  x nicolo_density.xyz
    x column: #sample center
    x row: density for each radius (7)
    x calculate NUC in MatLab, D sample centers
      x avg = repmat(sum(density)/D, D, 1)
      x NUC = sqrt(sum((density - avg).^2)/D)
